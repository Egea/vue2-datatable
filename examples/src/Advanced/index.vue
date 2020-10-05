<template>
  <div>
    <p><code>query: {{ query }}</code></p>
    <datatable v-bind="$data" @on-row-click="rowClicked">
      <button class="btn btn-default" @click="alertSelectedUids" :disabled="!selection.length">
        <i class="fa fa-commenting-o"></i>
        Alert selected uid(s)
      </button>
      <template v-slot:spinner> Loading... </template>
      <template v-slot:other.country="col"><span style="color: darkred"><i>{{ col.data }}</i></span></template>
      <template v-slot:actions="col"><button @click="rowClicked(col.row)">Click me</button></template>
    </datatable>
  </div>
</template>
<script>
import Vue from 'vue'
import mockData from '../_mockData'
import components from './comps/'

export default {
  components,
  name: 'FriendsTable', // `name` is required as a recursive component
  props: ['row'], // from the parent FriendsTable (if exists)
  data () {
    const amINestedComp = !!this.row

    return {
      HeaderSettings: true,
      supportBackup: true,
      supportNested: true,
      enableSearchRow: true,
      tblClass: 'table-bordered',
      tblStyle: 'color: #666',
      pageSizeOptions: [5, 10, 15, 20],
      columns: (() => {
        const cols = [
          { title: 'UID', field: 'uid', label: 'User ID', sortable: true, visible: 'true' },
          { title: 'Email', field: 'email', visible: false, thComp: 'FilterTh', tdComp: 'Email', searchable: true },
          { title: 'Username', field: 'name', thComp: 'FilterTh', tdStyle: { fontStyle: 'italic' }, searchable: true },
          { title: 'Country', field: 'country', thStyle: { fontWeight: 'normal' }, searchable: true, searchOptions: [{label: 'Ru', value: 'Russia'}, {label: 'Ge', value: 'Germany'}, {label: 'Fr', value: 'France'}] },
          { title: 'IP', field: 'ip', visible: false, tdComp: 'IP' },
          { title: 'Age', field: 'age', sortable: true, thClass: 'text-info', tdClass: 'text-success' },
          { title: 'Create time', field: 'createTime', sortable: true, colClass: 'w-240', thComp: 'CreatetimeTh', tdComp: 'CreatetimeTd' },
          { title: 'Color', field: 'color', explain: 'Favorite color', visible: false, tdComp: 'Color' },
          { title: 'Language', field: 'other.lang', visible: false, thComp: 'FilterTh', searchable: true },
          { title: 'PL', field: 'other.programLang', explain: 'Programming Language', visible: false, thComp: 'FilterTh', searchable: true },
          { title: 'Operation', tdComp: 'Opt', visible: true, fireRowClick: false },
          { title: 'Actions', field: 'actions', visible: true, fireRowClick: false }
        ]
        const groupsDef = {
          Normal: ['Email', 'Username', 'Country', 'IP'],
          Sortable: ['UID', 'Age', 'Create time'],
          Extra: ['Operation', 'Color', 'Language', 'PL']
        }
        return cols.map(col => {
          Object.keys(groupsDef).forEach(groupName => {
            if (groupsDef[groupName].includes(col.title)) {
              col.group = groupName
            }
          })
          return col
        })
      })(),
      data: [],
      total: 0,
      selection: [],
      summary: {},

      // `query` will be initialized to `{ limit: 10, offset: 0, sort: '', order: '' }` by default
      // other query conditions should be either declared explicitly in the following or set with `Vue.set / $vm.$set` manually later
      // otherwise, the new added properties would not be reactive
      query: amINestedComp ? { uid: this.row.friends } : {},

      // any other staff that you want to pass to dynamic components (thComp / tdComp / nested components)
      xprops: {
        eventbus: new Vue()
      },
      loading: true
    }
  },
  watch: {
    query: {
      async handler () {
        this.loading = true
        await this.sleep(2000).then(() => {
          this.handleQueryChange()
          this.loading = false
        })
      },
      deep: true
    }
  },
  methods: {
    handleQueryChange () {
      mockData(this.query).then(({ rows, total, summary }) => {
        this.data = rows
        this.total = total
        this.summary = summary
      })
    },
    alertSelectedUids () {
      alert(this.selection.map(({ uid }) => uid))
    },
    rowClicked (row) {
      alert(row.uid + '  ' + row.name)
    },
    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }
  }
}
</script>
<style>
.w-240 {
  width: 240px;
}
</style>
