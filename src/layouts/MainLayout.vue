<template>
  <q-layout>
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        />

        <q-toolbar-title>
          Suitcarrer - Contact List
        </q-toolbar-title>
      </q-toolbar>
    </q-header>
    <q-page-container padding>
      <q-page>
        <div class="q-pa-md row">
          <div class="col-md-5 offset-md-3">
            <q-banner class="bg-primary text-white q-mb-md text-center">
              <h6>Contact List</h6>
            </q-banner>
            <q-search
              v-model="searchModel"
              :debounce="600"
              placeholder="Hotels"
              icon="local_hotel"
              float-label="What is your hotel?"
            />
            <q-table
              :fullscreen="false"
              title="Treats"
              :data="contacts"
              :columns="colums"
              row-key="contacts"
              :pagination="initialPagination"
              no-data-label="I didn't find anything for you"
            >
              <template slot="top-right">
                <q-btn
                  @click="isOpenDialog = true"
                  icon="add_box"
                  size="14px"
                  color="secondary"
                  class="q-mr-sm"
                  label="Add Data"/>
              </template>
              <template slot="body" slot-scope="props">
                <q-tr :props="props">
                  <q-td key="name" :props="props">
                    {{ props.row.name }}
                  </q-td>
                  <q-td key="phone" :props="props">
                    {{ props.row.phone }}
                  </q-td>
                  <q-td key="action" :props="props">
                    <q-btn
                      @click="getDataById(props.row.id)"
                      icon="edit"
                      size="10px"
                      color="primary"
                      class="q-mr-sm"
                      label="Edit"/>
                    <q-btn
                      @click="getDelConfirm(props.row)"
                      icon="delete"
                      size="10px"
                      color="negative"
                      label="Delete"/>
                  </q-td>
                </q-tr>
              </template>
            </q-table>
          </div>
        </div>
      </q-page>
      <q-dialog v-model="isOpenDialog">
        <q-card class="responsive-width">
          <q-card-section class="row items-center q-pb-none">
            <div class="text-h6">Contact List</div>
            <q-space/>
            <q-btn icon="close" flat round dense v-close-popup/>
          </q-card-section>

          <q-card-section>
            <q-card class="my-card" flat bordered>
              <q-item>
                <q-item-section avatar>
                  <q-avatar>
                    <img src="https://cdn.quasar.dev/img/boy-avatar.png" alt="profile">
                  </q-avatar>
                </q-item-section>
                <q-item-section>
                  <q-item-label>{{ dialogData.name }}</q-item-label>
                  <q-item-label caption>
                    Subhead
                  </q-item-label>
                </q-item-section>
              </q-item>

              <q-separator/>

              <q-card-section>
                <q-card-section>
                  <q-input v-model="dialogData.name" label="Name" :dense="false"/>
                  <q-input v-model="dialogData.phone" label="Phone" :dense="false"/>
                </q-card-section>
              </q-card-section>
              <q-card-actions align="right" class="bg-white text-teal">
                <q-btn flat label="Cancel" v-close-popup/>
                <q-btn flat :label="dialogData.id ? 'Update': 'Tambah'"
                       @click="dialogData.id === null ? addData() : updateDataAPI(dialogData.id)"
                       class="bg-positive text-white"/>
              </q-card-actions>
            </q-card>
          </q-card-section>
        </q-card>
      </q-dialog>
      <q-dialog v-model="confirm" persistent>
        <q-card>
          <q-card-section class="row items-center">
            <q-avatar icon="delete" color="negative" text-color="white"/>
            <span class="q-ml-sm">You are sure want to delete {{ dialogData.name }} ?</span>
          </q-card-section>

          <q-card-actions align="right">
            <q-btn flat label="Cancel" color="primary" v-close-popup/>
            <q-btn flat label="Delete" color="primary" @click="delDataById(dialogData.id)"/>
          </q-card-actions>
        </q-card>
      </q-dialog>

    </q-page-container>
  </q-layout>
</template>

<script>
import axios from 'axios'

export default {
  name: 'MainLayout',
  data () {
    return {
      leftDrawerOpen: false,
      contacts: [],
      colums: [
        {
          name: 'name',
          required: true,
          label: 'Name',
          align: 'left',
          field: row => row.name,
          format: val => `${val}`,
          sortable: true,
          style: 'width: 500px'
        },
        {
          name: 'phone',
          required: true,
          label: 'Phone',
          align: 'left',
          field: row => row.phone,
          format: val => `${val}`,
          sortable: true,
          style: 'width: 500px'
        },
        {
          name: 'action',
          required: true,
          label: 'Action',
          align: 'center',
          field: null,
          format: val => `${val}`,
          sortable: true,
          style: 'width: 500px'
        }
      ],
      initialPagination: {
        sortBy: 'desc',
        descending: false,
        page: 1,
        rowsPerPage: 10
      },
      isOpenDialog: false,
      dialogData: {
        name: 'John Doe',
        phone: null,
        id: null
      },
      confirm: false
    }
  },
  created () {
    this.initDataAPI()
  },
  methods: {
    initDataAPI: function () {
      axios.get('http://localhost:3000/contacts')
        .then(res => {
          if (res.status === 200) {
            this.contacts = res.data
          } else {
            throw new Error()
          }
        })
        .catch(res => {
          console.log('error', res)
        })
    },
    getDataById: function (id) {
      this.isOpenDialog = !this.isOpenDialog
      this.dialogData.id = id
      axios.get('http://localhost:3000/contacts/' + id)
        .then(res => {
          if (res.status === 200) {
            this.dialogData.name = res.data.name
            this.dialogData.phone = res.data.phone
          } else {
            throw new Error()
          }
        })
        .catch(res => {
          console.log('error', res)
        })
    },
    updateDataAPI: function (id) {
      const data = {
        name: this.dialogData.name,
        phone: this.dialogData.phone
      }
      axios.put('http://localhost:3000/contacts/' + id, data)
        .then(res => {
          this.setResult(res.status, 'Data Successfully Changed', 'positive')
        })
        .catch(res => {
          console.log('error', res)
        })
    },
    delDataById: function (id) {
      axios.delete('http://localhost:3000/contacts/' + id)
        .then(res => {
          this.setResult(res.status, 'Data Successfully Deleted', 'negative')
        })
        .catch(res => {
          console.log('error', res)
        })
    },
    addData: function () {
      const data = {
        name: this.dialogData.name,
        phone: this.dialogData.phone
      }
      if (data.name.length && data.phone !== null) {
        axios.post('http://localhost:3000/contacts/', data)
          .then(res => {
            this.setResult(res.status, 'Data Successfully Added', 'positive')
          })
          .catch(res => {
            console.log('error', res)
          })
      } else {
        console.log('masuk')
        this.$q.notify({
          progress: true,
          message: 'Data Shouldn\'t be blank',
          color: 'negative',
          multiLine: true,
          avatar: 'https://cdn.quasar.dev/img/boy-avatar.png'
        })
      }
    },
    getDelConfirm: function (val) {
      this.dialogData.name = val.name
      this.dialogData.phone = val.phone
      this.dialogData.id = val.id
      this.confirm = !this.confirm
    },
    setResult: function (res, msg, color) {
      if (res === 200 || res === 201) {
        this.$q.notify({
          progress: true,
          message: msg,
          color: color,
          multiLine: true,
          avatar: 'https://cdn.quasar.dev/img/boy-avatar.png'
        })
        setTimeout(() => {
          this.isOpenDialog = false
          window.location.reload()
        }, 1500)
      } else {
        throw new Error()
      }
    }
  }
}
</script>

<style lang="scss">
@import "src/css/app";
</style>
