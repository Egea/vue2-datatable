<template>
  <tbody>
    <tr v-if="enableSearchRow">
      <td v-if="shouldRenderSelection" /> <!-- Checkbox for rows -->
      <td v-for="col in columns"> <!-- Custom search with input or dropdown -->
        <v-select v-if="col.searchable && col.searchOptions" :options="col.searchOptions" :disabled="loading"
                  v-model="keyword[col.searchField ? col.searchField : col.field]" />
        <input v-else-if="col.searchable" type="search" class="form-control table-search-input"
               ref="input" v-model="keyword[col.searchField ? col.searchField : col.field]" :disabled="loading">
      </td>
    </tr>
    <tr v-if="loading">
      <td :colspan="colLen" class="text-center">
        <slot name="spinner" />
      </td>
    </tr>
    <template v-else-if="data.length">
      <template v-for="item in data">
        <tr>
          <td v-if="shouldRenderSelection">
            <multi-select :selection="selection" :row="item" />
          </td>
          <td v-for="col in columns" :class="[col.tdClass, {'cursor-pointer': clickableColumn(col)}]"
              :style="col.tdStyle" @click.capture="rowClicked(item, col)">
            <!-- <td> component (tdComp) -->
            <component
              v-if="col.tdComp"
              :is="forDynCompIs(col.tdComp)"
              :row="item"
              :field="col.field"
              :value="item[col.field]"
              :nested="item.__nested__"
              v-bind="$props">
            </component>
            <template v-else-if="$scopedSlots[col.field]">
              <template>
                <slot :name="col.field" :data="getField(item, col.field)" :row="item"/>
              </template>
            </template>
            <template v-else>
              {{ getField(item, col.field) }}
            </template>
          </td>
        </tr>
        <transition name="fade">
          <tr v-if="item.__nested__ && item.__nested__.visible">
            <td :colspan="colLen">
              <!-- nested component -->
              <component
                :is="forDynCompIs(item.__nested__.comp)"
                :row="item"
                :nested="item.__nested__"
                v-bind="$props">
              </component>
            </td>
          </tr>
        </transition>
      </template>
    </template>
    <tr v-else-if="!leftFixed && !rightFixed">
      <td :colspan="colLen" class="text-center text-muted">
        ( {{ $i18nForDatatable('No Data') }} )
      </td>
    </tr>
  </tbody>
</template>
<script>
import MultiSelect from './MultiSelect.vue'
import props from '../_mixins/props'
import shouldRenderSelection from '../_mixins/shouldRenderSelection'
import _ from 'lodash'
import vSelect from 'vue-select'

export default {
  name: 'TableBody',
  components: { MultiSelect, vSelect },
  mixins: [props, shouldRenderSelection],
  computed: {
    colLen () {
      return this.columns.length + !!this.selection
    }
  },
  data() {
    return {
      keyword: {}
    }
  },
  mounted() {
    // Check if a default value is to be set for the text or dropdown filters (from the default query)
    let keywords = {}
    this.columns.filter(col => this.query[col.searchField ? col.searchField : col.field]).forEach(col => {
      if (col.searchOptions) {
        keywords[col.searchField ? col.searchField : col.field] = col.searchOptions.find(option => option.value === this.query[col.searchField ? col.searchField : col.field])
      } else {
        keywords[col.searchField ? col.searchField : col.field] = this.query[col.searchField ? col.searchField : col.field]
      }
    })
    this.$set(this, 'keyword', keywords)
  },
  methods: {
    getField(obj, path) {
      return _.get(obj, path)
    },
    clickableColumn(col) {
      return this.enableClickableRows && (col.fireRowClick === undefined || col.fireRowClick)
    },
    rowClicked(row, cell) {
      if (this.clickableColumn(cell)) {
        this.$emit('on-row-click', row)
      }
    },
    search: _.debounce(function () {
      // `$props.query` would be initialized to `{ limit: 10, offset: 0, sort: '', order: '' }` by default
      // custom query conditions must be set to observable by using `Vue.set / $vm.$set`
      const keywords = {...this.keyword}
      for (const field in keywords) {
        if (this.keyword[field]) {
          this.$set(this.query, field, typeof this.keyword[field] === 'object' ? this.keyword[field].value : this.keyword[field])
        } else {
          // empty string search
          this.$delete(this.query, field)
          delete this.keyword[field]
        }
      }
      this.query.offset = 0 // reset pagination
    }, 500)
  },
  watch: {
    keyword: {
      deep: true,
      handler(val) {
        this.search()
      }
    }
  }
}
</script>
<style>
  .cursor-pointer {
    cursor: pointer;
  }
  .table-search-input {
    border-radius: 0;
    padding: 0 12px;
  }
</style>
