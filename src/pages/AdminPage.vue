<template>
  <div class="background">
  
    <import-cats />
    <BreedsTable />
    <q-separator />
    <UsersTable />
	<h4>
      Crons ({{ parsedLogs.length }}) - Last state
    </h4>
	<div class="q-pa-md">
    
    <q-table
      v-if="parsedLogs.length"
      :rows="parsedLogs"
      :columns="columns"
      row-key="timestamp"
      class="table-crons"
      virtual-scroll
    >
      <template v-slot:body="props">
      <q-tr :props="props">
        <q-td key="id" :props="props">{{ props.row.id }}</q-td>
        <q-td key="cron" :props="props">{{ props.row.cron }}</q-td>
        <q-td key="timestamp" :props="props">{{ props.row.timestamp }}</q-td>

        <q-td key="event" :props="props">
          <q-icon v-if="props.row.event === 'STARTED'" size="md" name="downloading" color="green">
            <q-tooltip>{{ props.row.event }}</q-tooltip>
          </q-icon>
          
		  <q-icon v-else-if="props.row.event === 'STOPPED'" size="md" name="remove_circle" color="red" material-icons-outlined>
            <q-tooltip>{{ props.row.event }}</q-tooltip>
		  </q-icon>
		  
		  <q-icon v-else-if="props.row.event === 'DOWNLOADED'" size="md" name="download_done" color="green" material-icons-outlined>
            <q-tooltip>{{ props.row.event }}</q-tooltip>
          </q-icon>
          
		  <q-icon v-else round size="md" name="error" color="red">
		  <q-tooltip>{{ props.row.event }}</q-tooltip>
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
  </div>
  
  <h4>
    All Crons ({{ parsedLogsV1.length }})
    </h4>
	<div class="q-pa-md">
    
    <q-table
      v-if="parsedLogsV1.length"
      :rows="parsedLogsV1"
      :columns="columns"
      row-key="timestamp"
      class="table-crons"
      virtual-scroll
    >
      <template v-slot:body="props">
      <q-tr :props="props">
        <q-td key="id" :props="props">{{ props.row.id }}</q-td>
        <q-td key="cron" :props="props">{{ props.row.cron }}</q-td>
        <q-td key="timestamp" :props="props">{{ props.row.timestamp }}</q-td>

        <q-td key="event" :props="props">
          <q-icon v-if="props.row.event === 'STARTED'" size="md" name="downloading" color="green">
            <q-tooltip>{{ props.row.event }}</q-tooltip>
          </q-icon>
          
		  <q-icon v-else-if="props.row.event === 'STOPPED'" size="md" name="remove_circle" color="red" material-icons-outlined>
            <q-tooltip>{{ props.row.event }}</q-tooltip>
		  </q-icon>
		  
		  <q-icon v-else-if="props.row.event === 'DOWNLOADED'" size="md" name="download_done" color="green" material-icons-outlined>
            <q-tooltip>{{ props.row.event }}</q-tooltip>
          </q-icon>
		  
		  <q-icon v-else round size="md" name="error" color="red">
		  <q-tooltip>{{ props.row.event }}</q-tooltip>
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
  </div>
  </div>
</template>

<script lang="ts">
import UsersTable from '../components/tables/UsersTable.vue';
import { defineComponent } from 'vue';
import BreedsTable from 'src/components/tables/BreedsTable.vue';
import ImportCats from 'src/components/ImportCats.vue';
import { StatsService } from 'src/services';

const columns =
  [
    {name: 'id', required: true, label: 'ID', align: 'center', classes: 'column-id',},
    {name: 'cron', required: true, label: 'Name_of_Cron', align: 'left', field: (row: any) => row.cron, sortable: true, classes: 'column-cron',},
    {name: 'timestamp', required: true, label: 'Last_Date', align: 'center', field: (row: any) => row.timestamp, sortable: true, classes: 'column-timestamp',},
    {name: 'event', required: true, label: 'State', align: 'center', field: (row: any) => row.event, sortable: true, classes: 'column-event',},
    {name: 'open', label: 'Log', align: 'center', field: (row: any) => row.open, classes: 'column-value',}
  ]

export default defineComponent({
  name: 'AdminPage',
  components: { UsersTable, BreedsTable, ImportCats},
 
  data() {
    return {
      parsedLogs: [],
      parsedLogsV1: [],
      parsedLogsV2: [],
      parsedLogsV3: [],
	  columns
    };
  },
  async mounted() {
    await this.fetchLogs();
    await this.fetchLogsV2();
  },
  methods: {
  
  
  
  
  async fetchLogs() {
	  const eventTypes = ['downloaded', 'error', 'stopped', 'started'];

	 // Inicializácia prázdneho objektu pre zoskupenie dát
	 const groupedLogs = {};

	 // Prechádzanie typov eventov
	 for (const eventType of eventTypes) {
	  // Získanie záznamov pre daný typ eventu
	  const logs = await StatsService.getLogsByEvent(eventType);

	  // Spracovanie záznamov
	  if (logs.data && logs.data.length > 0) {
	    logs.data.forEach(logEntry => {
	     const key = logEntry.cron; // Kľúč pre zoskupenie - cron
	     const timestamp = new Date(logEntry.timestamp); // Timestamp záznamu

	     // Aktualizácia informácií v `groupedLogs`
	     if (!groupedLogs[key] || timestamp > groupedLogs[key].timestamp) {
	        groupedLogs[key] = {
			 id: logEntry.id,
	         value: logEntry.value,
	         timestamp: timestamp.toLocaleString(), // Formátovaný timestamp
	         cron: logEntry.cron,
	         event: logEntry.event
	        };
	     }
	    });
	  }
	 }

	 // Konverzia zoskupených záznamov na pole a triedenie podľa timestampu
	 this.parsedLogs = Object.values(groupedLogs).sort((a, b) => b.timestamp - a.timestamp);

	 // Kontrola prázdnoty
	 if (this.parsedLogs.length === 0) {
	  console.log('No logs found');
	 }

	 return this.parsedLogs;
	},
	
	async fetchLogsV2() {
  const eventTypes = ['downloaded'];

  // Inicializácia prázdneho objektu pre zoskupenie dát
  const groupedLogsV2 = {};

  // Prechádzanie typov eventov
  for (const eventType of eventTypes) {
    // Získanie záznamov pre daný typ eventu
    const logs = await StatsService.getLogsByEvent(eventType);

    // Spracovanie záznamov
    if (logs.data && logs.data.length > 0) {
      logs.data.forEach(logEntry => {
        const key = logEntry.cron; // Kľúč pre zoskupenie - cron
        const timestamp = new Date(logEntry.timestamp); // Timestamp záznamu

        // Ak ešte neexistuje záznam pre daný kľúč, vytvorte pole
        if (!groupedLogsV2[key]) {
          groupedLogsV2[key] = [];
        }

        // Pridanie nového záznamu do poľa pre daný kľúč
        groupedLogsV2[key].push({
          id: logEntry.id,
          value: logEntry.value,
          timestamp: timestamp.toLocaleString(), // Formátovaný timestamp
          cron: logEntry.cron,
          event: logEntry.event
        });
      });
    }
  }

  // Konverzia zoskupených záznamov na pole a triedenie podľa timestampu
  this.parsedLogsV1 = Object.values(groupedLogsV2).flatMap(logs => logs).sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp));

  // Kontrola prázdnoty
  if (this.parsedLogsV1.length === 0) {
    console.log('No logs found');
  }

  return this.parsedLogsV1;
}
  },
});
</script>

<style lang="scss" scoped>
.background {
  padding: 24px;
  max-width: 1440px;
  width: 100%;
  margin: 0 auto;
  @media (max-width: 575.98px) {
    padding: 0;
  }
}
</style>
