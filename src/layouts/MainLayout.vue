<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>

        <q-toolbar-title>
          ООП Загрузчик
        </q-toolbar-title>

        <div>LIT BGPU</div>
      </q-toolbar>
    </q-header>



    <q-page-container v-if="documents">
      <div class="q-pa-md" >
        <q-table
          flat
          :filter="filter"
          :rows-per-page-options="[0]"
          :rows="documents"
          :columns="columns"
          row-key="id"
          dense
          separator="horizontal"
        >
          <template v-slot:header="props">
            <q-tr :props="props">
              <q-th auto-width />
              <q-th
                v-for="col in props.cols"
                :key="col.name"
                :props="props"
              >
                {{ col.label }}
              </q-th>
            </q-tr>
          </template>
          <template v-slot:body="props">
            <q-tr :props="props">
              <q-td auto-width>
                <q-btn flat @click.stop="download(props.row.id)" label="Скачать"/>
              </q-td>
              <q-td
                auto-width
                v-for="col in props.cols"
                :key="col.name"
                :props="props"
                @click="download(props.row.id)"
                style="cursor: pointer"
              >
                {{ col.value }}
              </q-td>
            </q-tr>
          </template>
          <template v-slot:top-right>
            <q-input outlined style="width: 600px" dense debounce="300" v-model="filter" placeholder="Поиск">
              <template v-slot:append>
                <q-icon name="search" />
              </template>
            </q-input>
          </template>
        </q-table>
      </div>

    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'
import axios from "axios";
import { useQuasar } from 'quasar'



export default defineComponent({
  name: 'MainLayout',
  setup() {
    return {
      filter: ref('')
    }
  },
  data() {
    return {
      $q: useQuasar(),
      documents: null,
      pagination: {
        rowsPerPage: 100
      },
      columns: [
        {name: 'dirname', label: 'Направление', field: 'dirname', sortable: true, align: 'left'},
        {name: 'name', label: 'Название', field: 'name', sortable: true,  align: 'left'},
        {name: 'facultyShortTitle', label: 'Факультет', field: 'facultyShortTitle', sortable: true,  align: 'left'},
        {name: 'shifer', label: 'Шифр', field: 'shifer', sortable: true,  align: 'left'},
      ],
    };
  },
  methods:{
    test(){
      this.$q.loading.show({
        message: 'Some important process  is in progress. Hang on...'
      })
    },
    download(id){
      this.$q.loading.show({
        message: 'Идёт загрузка файла, подождите...'
      })
      axios({
        method: 'GET',
        url: `https://192.168.202.115:8080/program/${id}`,
        responseType: 'blob'
      }).then(response => {
        console.log(response)
        let blob = new Blob([response.data], { type: 'application/zip' })
        let link = document.createElement('a')
        link.href = window.URL.createObjectURL(blob)
        document.body.appendChild(link);
        link.click()
        document.body.removeChild(link);
        this.$q.loading.hide()
      })
    }

  },
  mounted() {
    axios.get('https://192.168.202.115:8080/').then(response => {
      console.log(response)
      this.documents = response.data
    })
  }


})
</script>
