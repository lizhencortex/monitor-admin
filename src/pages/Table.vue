<template>
  <div class="cortex-main-table">
    <q-table
      title="Monitor"
      :data="rows"
      :columns="columns"
      row-key="field"
      wrap-cells
      dense
      auto-width
      :pagination.sync="pagination"
      wrap-cells
    >
      <template slot="body" slot-scope="props">
        <q-tr :props="props" @click.native="viewDetail(props.row)">
          <q-td key="ip" :props="props">{{props.row.ip}}</q-td>
          <q-td key="location" :props="props">{{props.row.location}}</q-td>
          <q-td key="cpu" :props="props">{{props.row.cpu}}</q-td>
          <q-td key="blocknum" :props="props">{{props.row.blocknum}}</q-td>
          <q-td key="mem" :props="props">{{props.row.mem}}</q-td>
          <q-td key="gpunum" :props="props">{{props.row.gpunum}}</q-td>
          <q-td key="gputemp" :props="props">{{props.row.gputemp}}</q-td>
          <q-td key="gpupower" :props="props">{{props.row.gpupower}}</q-td>
          <q-td key="gpufan" :props="props">{{props.row.gpufan}}</q-td>
          <q-td key="time" :props="props">{{props.row.time}}</q-td>
        </q-tr>

        <tr v-show="props.row.expand" :props="props">
          <td colspan="100">
            <div>

              <q-table
                class="cortex-detail-table"
                title="GPU Detail"
                :data="props.row.gpudetail"
                :columns="gpuDetailColumns"
                row-key="field"
                auto-width
                wrap-cells
                dense
              >
              </q-table>

              <q-list highlight>
                <q-list-header>Gpu Info</q-list-header>
                <q-item v-for="(value, key) in props.row.gpuinfo" :key="key">
                  <q-item-side>{{key}}</q-item-side>
                  <q-item-main class="cortex-detail-item-main">{{value}}</q-item-main>
                </q-item>
              </q-list>

              <!-- <q-list highlight>
                <q-list-header>IP Geography</q-list-header>
                <q-item v-for="(value, key) in props.row.ipgeo" :key="key">
                  <q-item-side>{{key}}</q-item-side>
                  <q-item-main class="cortex-detail-item-main">{{value}}</q-item-main>
                </q-item>
              </q-list> -->

              <!-- <q-list highlight>
                <q-list-header>Logs</q-list-header>
                <q-item v-for="(value, key) in props.row.logs" :key="key">
                  <q-item-side>{{key}}</q-item-side>
                  <q-item-main class="cortex-detail-item-main">{{value}}</q-item-main>
                </q-item>
              </q-list> -->

              <q-list highlight>
                <q-item>
                  <q-item-side>mac</q-item-side>
                  <q-item-main>{{props.row.mac}}</q-item-main>
                </q-item>
              </q-list>

              <q-list highlight>
                <q-list-header>System Info</q-list-header>
                <q-item v-for="(value, key) in props.row.sysinfo" :key="key">
                  <q-item-side>{{key}}</q-item-side>
                  <q-item-main class="cortex-detail-item-main">{{value}}</q-item-main>
                </q-item>
              </q-list>
            </div>
          </td>
        </tr>
      </template>
    </q-table>
  </div>
</template>

<script>
export default {
  name: 'Table',
  props: [ 'syncdata' ],
  watch: {
    syncdata(val) {
        const data = this.syncdata
        const expands = this.rows.map(item => item.expand)
        this.rows = []
        for (let i=0; i<data.length; i++) {
            let x = data[i]
            let cpuinfo = x.sysinfo.cpu_overview.replace(/^\s+|\s+$/gm,'')
            let cpuinfo_split = cpuinfo.indexOf(' ')
            cpuinfo = `${cpuinfo.slice(cpuinfo_split + 1)}, ${cpuinfo.slice(0, cpuinfo_split)} ths`
            cpuinfo = cpuinfo.replace(' CPU', '').replace(' Threadripper', '').replace(' Processor', '')

            const t = new Date(x.time)

            let mem = x.sysinfo.memory_overview.match(/([1-9][0-9]*)/g)

            let gputemp = x.gpudetail.map(d => d['GPU Current Temp'].replace('C', '').replace(/^\s+|\s+$/gm,'')).join('/ ')

            let gpupower = x.gpudetail.map(d => ~~(+d['Power Draw'].replace('W', ''))).join('/ ')

            let gpufan = x.gpudetail.map(d => !d['Fan Speed'] ? 'N/A' : d['Fan Speed'].replace('%', '').replace(/^\s+|\s+$/gm,'')).join('/ ')

            this.rows.push({
                ip: x.ip, //
                lip: x.sysinfo.localip.replace(/inet addr\:/g, '').replace(/inet/g, '').replace(/addr:/g, ''),
                location: `${x.ipgeo.countryCode}, ${x.ipgeo.region}, ${x.ipgeo.city}`, //
                mac: x.mac,
                cpu: cpuinfo,
                blocknum: x.sysinfo.blocknum, //
                totalMem: mem[0],
                usedMem: mem[1],
                availMem: mem[5],
                gpunum: x.gpuinfo['Attached GPUs'], //
                gputemp: gputemp, //
                gpupower: gpupower, //
                gpufan: gpufan, //
                mem: `${mem[1]} / ${mem[0]}`, //
                time: t.toDateString().slice(4) + ", " + t.toTimeString().slice(0, 8), //

                gpudetail: x.gpudetail,
                gpuinfo: x.gpuinfo,
                ipgeo: x.ipgeo,
                sysinfo: x.sysinfo,
                logs: x.logs,
                expand: expands[i],
            })
        }
    }
  },
  methods: {
    viewDetail(row) {
      this.$set(row, 'expand', !row.expand)
    }
  },
  data () {
    return {
      pagination: {
        page: 1,
        rowsPerPage: 15,
      },
      columns: [
        {
          label: 'Global IP',
          field: 'ip',
          name: 'ip',
        },
        {
          label: 'Location',
          field: 'location',
          name: 'location',
        },
        {
          label: 'CPU Info',
          field: 'cpu',
          name: 'cpu',
        },
        {
          label: 'Block',
          field: 'blocknum',
          name: 'blocknum',
          type: 'number',
        },
        {
          label: 'RAM (MiB)',
          field: 'mem',
          name: 'mem',
        },
        {
          label: 'GPUs',
          field: 'gpunum',
          name: 'gpunum',
        },
        {
          label: 'GPU Temp (℃)',
          field: 'gputemp',
          name: 'gputemp',
        },
        {
          label: 'GPU Power (W)',
          field: 'gpupower',
          name: 'gpupower',
        },
        {
          label: 'GPU Fan (%)',
          field: 'gpufan',
          name: 'gpufan',
        },
        {
          label: 'Last Report Time',
          field: 'time',
          name: 'time',
        },
      ],
      rows: [
      ],
      gpuDetailColumns: [
        { label: 'Current', field: 'Current', },
        { label: 'Device Id', field: 'Device Id', },
        { label: 'Free', field: 'Free', },
        { label: 'GPU 00000000', field: 'GPU 00000000', },
        { label: 'GPU Current Temp', field: 'GPU Current Temp', },
        { label: 'GPU Shutdown Temp', field: 'GPU Shutdown Temp', },
        { label: 'GPU Slowdown Temp', field: 'GPU Slowdown Temp', },
        { label: 'GPU UUID', field: 'GPU UUID', },
        { label: 'Gpu', field: 'Gpu', },
        { label: 'Graphics', field: 'Graphics', },
        { label: 'Max', field: 'Max', },
        { label: 'Memory', field: 'Memory', },
        { label: 'Performance State', field: 'Performance State', },
        { label: 'Power Draw', field: 'Power Draw', },
        { label: 'Product Name', field: 'Product Name', },
        { label: 'Rx Throughput', field: 'Rx Throughput', },
        { label: 'SM', field: 'SM', },
        { label: 'Total', field: 'Total', },
        { label: 'Tx Throughput', field: 'Tx Throughput', },
        { label: 'Used', field: 'Used', },
        { label: 'Video', field: 'Video', },
      ],
    }
  }
}
</script>

<style>
.cortex-main-table {
  background: #eeeeee;
}
.cortex-detail-table th, .cortex-detail-table td {
  white-space: normal;
}
.cortex-detail-item-main {
  word-break: break-all;
  white-space: normal;
}
</style>
