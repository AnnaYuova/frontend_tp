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
        <q-td key="name" :props="props">{{ props.row.name }}</q-td>
:
        <q-td key="date_time" :props="props">{{ props.row.date_time }}</q-td>
        <q-td key="state" :props="props">
          <q-icon v-if="props.row.state === 'active'" size="md" name="circle" color="green">
            <q-tooltip>{{ props.row.state }}</q-tooltip>
          </q-icon>
          <q-icon v-else-if="props.row.state === 'stopped'" size="md" name="circle" color="red" material-icons-outlined>
            <q-tooltip>{{ props.row.state }}</q-tooltip>
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
import { defineComponent, ref, onMounted } from 'vue';
import { QTableProps } from 'quasar';
import { cacheStore } from 'src/stores/cache-store';
import { StatsService } from 'src/services';

const cron_list =
  [
    {
      name  : 'FDCAT',
      date_time : '2024-04-27T14:52:26.654Z',
      state : 'active',
      open: 0
    },
    {
      name  : 'Bengal',
      date_time : '2024-04-27T14:52:26.348Z',
      state : 'stopped',
      open: 0
    },
    {
      name: 'New_race',
      date_time : '2024-04-27T14:52:26.040Z',
      state : 'neutral',
      open: 0
    },
    {
      name  : 'Sibír',
      date_time : '2024-04-27T14:52:26.654Z',
      state : 'active',
      open: 0
    },
    {
      name  : 'English',
      date_time : '2024-04-27T14:52:26.348Z',
      state : 'stopped',
      open: 0
    },
    {
      name: 'Aztec',
      date_time : '2024-04-27T14:52:26.040Z',
      state : 'neutral',
      open: 0
    }
]

const columns =
  [
    {name: 'id', required: true, label: 'ID', align: 'center', classes: 'column-id',},
    {name: 'name', required: true, label: 'Name_of_Cron', align: 'left', field: (row: any) => row.name, sortable: true, classes: 'column-name',},
    {name: 'date_time', required: true, label: 'Last_Date', align: 'center', field: (row: any) => row.date_time, sortable: true, classes: 'column-date_time',},
    {name: 'state', required: true, label: 'State', align: 'center', field: (row: any) => row.state, sortable: true, classes: 'column-state',},
    {name: 'open', label: 'Log', align: 'center', field: (row: any) => row.open, classes: 'column-actions',}
  ]

let rows: any[] = []

const parsedLogs = ref([]);

async fetchLogs() {
  const eventTypes = ['downloaded', 'error', 'stopped', 'started'];
 const groupedLogs = {};
 for (const eventType of eventTypes) {
  const logs = await StatsService.getLogsByEvent(eventType);
  if (logs.data && logs.data.length > 0) {
    logs.data.forEach(logEntry => {
     const key = logEntry.cron;
     const timestamp = new Date(logEntry.timestamp);
     if (!groupedLogs[key] || timestamp > groupedLogs[key].timestamp) {
        groupedLogs[key] = {
         value: logEntry.value,
         timestamp: timestamp.toLocaleString(), // Formátovaný timestamp
         cron: logEntry.cron,
         event: logEntry.event
        };
     }
    });
  }
 }
 this.parsedLogs = Object.values(groupedLogs).sort((a, b) => b.timestamp - a.timestamp);
 if (this.parsedLogs.length === 0) {
  console.log('No logs found');
 }
 return this.parsedLogs;
}

export default {
  name: 'CronTable2',

  setup() {
	onMounted(async () => {
		parsedLogs.value = await fetchLogs();
		
		for (let i = 0; i < parsedLogs.length; i++) {
		  rows = rows.concat(parsedLogs.slice(0).map(r => ({ ...r })))
		}

		rows.forEach((row, id) => {
		  row.id = id
		})
	});
  
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
