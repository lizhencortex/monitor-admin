
<template>
  <q-list no-border class="bg-white">
    <q-list-header style="padding-right: 16px;"> Node Overview
      <span class="float-right">{{ status.checkTime }}</span>
    </q-list-header>
    <q-item>
      <q-item-side icon="arrow_upward" color="blue-4"/>
      <q-item-main>
        <q-item-tile label> Total Number
          <span class="float-right">{{ status.currentSubmit }}</span>
        </q-item-tile>
      </q-item-main>
    </q-item>
    <q-item>
      <q-item-side icon="check" color="green-4"/>
      <q-item-main>
        <q-item-tile label> Active Nodes
          <span class="float-right">{{ status.currentQualified }}</span>
        </q-item-tile>
      </q-item-main>
    </q-item>
    <q-item>
      <q-item-side icon="clear" color="red-4"/>
      <q-item-main>
        <q-item-tile label> Inactive nodes
          <span class="float-right">{{ status.currentUnqualified }}</span>
        </q-item-tile>
      </q-item-main>
    </q-item>
  </q-list>
</template>

<script>
  export default {
    name: 'Overview',
    props: [ 'syncdata' ],
    watch: {
        syncdata(val) {
            const data = this.syncdata
        }
    },
    data() {
        return {
            status: {},
        }
    },
    methods: {
    },
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main-overview {
  background: #eeeeee;
}
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
