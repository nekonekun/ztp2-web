<template>
  <q-page class="flex flex-center">
    <div class="q-pa-md">
      <div class="row">
      <q-input bottom-slots v-model="text" label="Заявка" :dense="dense" :loading="loadingState">

        <template v-slot:after>
          <q-btn round dense flat icon="search" @click="getEntries(text)"/>
        </template>
      </q-input>
      </div>
    
    <div class="row">
    <q-table
      title="Отчёт"
      :rows="entries"
      :columns="columns"
      row-key="ZTPID"
      v-model:pagination="pagination"
    />
    </div>
    </div>
  </q-page>
</template>

<script>
import { ref } from 'vue'
// import { api } from "boot/axios";
const columns = [
  { name: 'employee', label: 'Сотрудник', field: 'employee', align: 'left' },
  { name: 'model', label: 'Модель', field: 'model', align: 'left' },
  { name: 'serial', label: 'Серийный номер', field: 'serial', align: 'left' },
  { name: 'ip', label: 'IP адрес', field: 'ip', align: 'left', sortable: true },
  { name: 'mac', label: 'MAC адрес', field: 'mac', align: 'left' },
  { name: 'box', label: 'Ящик', field: 'box', align: 'left', sortable: true }
]

export default {
  setup () {
    return {
      text: ref(''),
      loadingState: ref(false),
      entries: ref([]),
      pagination: ref({
        rowsPerPage: 0
      }),
      columns
    }
  },
  methods: {
    getEntries(task_id) {
      const path = '/reports/commissioning/' + task_id + '/'
      this.loadingState = true;
      this.entries = []
      let added = []
      let newList = []
      return this.$api
        .get(path)
        .then((response) => {
          response.data.forEach(element => {
            if (added.indexOf(element['serial_number']) === -1) {
              added.push(element['serial_number'])
              let entry = {
                employee: element['employee'],
                model: element['model'],
                serial: element['serial_number'],
                ip: element['ip_address'],
                mac: element['mac_address'],
                box: element['box_name']
              }
              newList.push(entry)
              // console.log(element);
            }
          });
          this.loadingState = false;    
          newList.forEach(element => {console.log(element); this.entries.unshift(element)})
        });
    },
    truncate(value, length) {
      if (value.length > length) {
        return "..." + value.substring(value.length - length, value.length);
      } else {
        return value;
      }
    }
  }
}
</script>
