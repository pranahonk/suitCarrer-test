<template>
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
              <q-item-label>{{ custInfo.name }}</q-item-label>
              <q-item-label caption>
                Subhead
              </q-item-label>
            </q-item-section>
          </q-item>

          <q-separator/>

          <q-card-section>
            <q-card-section>
              <q-input v-model="custInfo.name" label="Name" :dense="false"/>
              <q-input v-model="custInfo.phone" label="Phone" :dense="false"/>
            </q-card-section>
          </q-card-section>
          <q-card-actions align="right" class="bg-white text-teal">
            <q-btn flat label="Cancel" v-close-popup/>
            <q-btn flat label="Update" @click="updateDataAPI" class="bg-positive text-white"/>
          </q-card-actions>
        </q-card>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>

<script>
export default {
  name: 'modal',
  props: {
    DataCust: {
      required: true,
      default: null,
      type: Object
    },
    isOpenDialog: {
      type: Boolean,
      required: true,
      default: false
    }
  },
  data: function () {
    return {
      custInfo: {
        name: null,
        phone: null
      }
    }
  },
  watch: {
    custInfo: {
      deep: true,
      handler (val) {
        this.$emit('updateDataCust', val)
      }
    },
    DataCust: function (val) {
      console.log(val)
      this.custInfo.name = val.name
      this.custInfo.phone = val.phone
    }
  },
  methods: {
    updateDataAPI: function () {
      this.$emit('updateDataAPI')
    }
  }
}
</script>

<style scoped>

</style>
