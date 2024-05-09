<template>
  <div class="table-header">
    <h4> Crons </h4>
    <div></div>
  </div>

  <!---
  <q-icon v-if="rows[0].state === 'active'" size="md" name="dangerous" color="red" r_thumb_up/>
  <q-icon size="md" name="check_circle" color="green" r_thumb_up/>
  <q-icon round size="md" name="window" r_thumb_up/>
  --->
  <q-table :rows="rows" :columns="columns" row-key="id" class="table-cron q-mb-lg">
    <template v-slot:body="props">
      <q-tr :props="props">
        <q-td key="id" :props="props">{{ props.row.id }}</q-td>
        <q-td key="cron" :props="props">{{ props.row.cron }}</q-td>
        <q-td key="timestamp" :props="props">{{ props.row.timestamp }}</q-td>

        <q-td key="event" :props="props">
          <q-icon v-if="props.row.event === 'active'" size="md" name="circle" color="green">
            <q-tooltip>{{ props.row.event }}</q-tooltip>
          </q-icon>
          <q-icon v-else-if="props.row.event === 'stopped'" size="md" name="circle" color="red" material-icons-outlined>
            <q-tooltip>{{ props.row.event }}</q-tooltip>
          </q-icon>
          <q-icon v-else round size="md" name="circle" color="yellow">
          </q-icon>
        </q-td>

        <q-td key="open" :props="props">
          <q-btn
            color="blue"
            icon-right='comment'
            no-caps
            flat
            dense
            @click="console.log('Hello there, I am groot')"
          />
        </q-td>

      </q-tr>
    </template>
  </q-table>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { QTableProps } from 'quasar';
import { cacheStore } from 'src/stores/cache-store';


const cron_list =
  [
    {
      cron  : 'FDCAT',
      timestamp : '2024-04-27T14:52:26.654Z',
      event : 'active',
      open: 0
    },
    {
      cron  : 'Bengal',
      timestamp : '2024-04-27T14:52:26.348Z',
      event : 'stopped',
      open: 0
    },
    {
      cron: 'New_race',
      timestamp : '2024-04-27T14:52:26.040Z',
      event : 'neutral',
      open: 0
    },
    {
      cron  : 'Sibír',
      timestamp : '2024-04-27T14:52:26.654Z',
      event : 'active',
      open: 0
    },
    {
      cron  : 'English',
      timestamp : '2024-04-27T14:52:26.348Z',
      event : 'stopped',
      open: 0
    },
    {
      cron: 'Aztec',
      timestamp : '2024-04-27T14:52:26.040Z',
      event : 'neutral',
      open: 0
    }
]


const columns =
  [
    {name: 'id', required: true, label: 'ID', align: 'center', classes: 'column-id',},
    {name: 'cron', required: true, label: 'Name_of_Cron', align: 'left', field: (row: any) => row.cron, sortable: true, classes: 'column-cron',},
    {name: 'timestamp', required: true, label: 'Last_Date', align: 'center', field: (row: any) => row.timestamp, sortable: true, classes: 'column-timestamp',},
    {name: 'event', required: true, label: 'State', align: 'center', field: (row: any) => row.event, sortable: true, classes: 'column-event',},
    {name: 'open', label: 'Log', align: 'center', field: (row: any) => row.open, classes: 'column-actions',}
  ]
let rows: any[] = []
for (let i = 0; i < cron_list.length; i++) {
  rows = rows.concat(cron_list.slice(0).map(r => ({ ...r })))
}
rows.forEach((row, id) => {
  row.id = id
})

export default {
  name: 'CronTable2',

  setup() {
    return {
      columns,
      rows,

      pagination: ref({
        rowsPerPage: 0
      })
    }
  }
}
</script>





<style lang="sass">
.my-sticky-virtscroll-table
  /* height or max-height is important */
  height: 410px

  .q-table__top,
  .q-table__bottom,
  thead tr:first-child th /* bg color is important for th; just specify one */
    background-color: #00b4ff

  thead tr th
    position: sticky
    z-index: 1
  /* this will be the loading indicator */
  thead tr:last-child th
    /* height of all previous header rows */
    top: 48px
  thead tr:first-child th
    top: 0

  /* prevent scrolling behind sticky top row on focus */
  tbody
    /* height of all previous header rows */
    scroll-margin-top: 48px
</style>
