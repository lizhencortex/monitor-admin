
<template>
  <q-page class="row layout-padding">
    <div class="col-md-12 q-pa-sm"> <com-table :syncdata="syncdata"/> </div>
  </q-page>
</template>

<script>
  import axios from 'axios'

  const comOverview = () => import("pages/Overview.vue")
  const comTable = () => import("pages/Table.vue")
  export default {
    components: { comOverview, comTable },
    data () {
      return {
        syncdata: null,
      }
    },
    async mounted() {
      const refresh = async () => {
        const response = await axios.get('http://monitor.cortexlabs.ai/api/show')
        this.syncdata = response.data
        console.log(this.syncdata)
      };
      refresh()
      setInterval(refresh, 60000)
    },
  };
</script>

<style scoped>
  .q-layout-page >>> .q-item-side {
    min-width: 0;
  }
</style>
