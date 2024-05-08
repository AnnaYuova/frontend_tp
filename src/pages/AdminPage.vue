<template>
  <div class="background">
    <import-cats />
    <BreedsTable />
    <q-separator />
    <UsersTable />
	<h4>
      Crons ({{ parsedLogs.length }})
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
      <template v-slot:header="props">
        <q-tr :props="props">
          <q-th
            v-for="col in props.cols"
            :key="col.name"
            :props="props"
            :class="'column-' + col.name"
          >
            {{ col.label }}
          </q-th>
        </q-tr>
      </template>
    </q-table>
  </div>
	<br/>
	

  </div>
</template>

<script lang="ts">
import UsersTable from '../components/tables/UsersTable.vue';
import { defineComponent } from 'vue';
import BreedsTable from 'src/components/tables/BreedsTable.vue';
import ImportCats from 'src/components/ImportCats.vue';
import { StatsService } from 'src/services';

export default defineComponent({
  name: 'AdminPage',
  components: { UsersTable, BreedsTable, ImportCats },
  data() {
    return {
      parsedLogs: [],
      parsedLogsV1: [],
      parsedLogsV2: [],
      parsedLogsV3: [],
    };
  },
  async mounted() {
    await this.fetchLogs();
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
