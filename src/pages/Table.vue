<template>
  <div class="main-table">
    <q-table
      title="Monitor"
      :data="rows"
      :columns="columns"
      row-key="field"
      wrap-cells
      dense
      auto-width
      :pagination.sync="pagination"
    />
  </div>
</template>

<script>
export default {
  name: 'Table',
  props: [ 'syncdata' ],
  watch: {
    syncdata(val) {
        const data = this.syncdata
        console.log(this.syncdata)
        this.rows = []
        for (let x of data) {
            let cpuinfo = x.sysinfo.cpu_overview.replace(/^\s+|\s+$/gm,'')
            let cpuinfo_split = cpuinfo.indexOf(' ')
            cpuinfo = `${cpuinfo.slice(cpuinfo_split + 1)}, ${cpuinfo.slice(0, cpuinfo_split)} cores`
            const t = new Date(x.time)
            let mem = x.sysinfo.memory_overview.match(/([1-9][0-9]*)/g)
            this.rows.push({
                ip: x.ip,
                lip: x.sysinfo.localip.replace(/inet addr\:/g, '').replace(/inet/g, '').replace(/addr:/g, ''),
                location: `${x.ipgeo.country}, ${x.ipgeo.regionName}, ${x.ipgeo.city}`,
                mac: x.mac,
                cpu: cpuinfo,
                blocknum: x.sysinfo.blocknum,
                totalMem: mem[0],
                usedMem: mem[1],
                availMem: mem[5],
                gpunum: x.gpuinfo['Attached GPUs'],
                gputemp: x.gpudetail.map(d => d['GPU Current Temp'].replace('C', '℃')).join(' / '),
                gpupower: x.gpudetail.map(d => d['Power Draw'].replace('C', '℃')).join(' / '),
                mem: `${mem[1]} / ${mem[0]} MiB`,
                time: t.toDateString() + ", " + t.toTimeString().slice(0, 8),
            })
        }
    }
  },
  data () {
    return {
      pagination: {
        sortBy: 'name',
        descending: false,
        page: 1,
        rowsPerPage: 10,
        rowsNumber: 10
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
          label: 'Memory',
          field: 'mem',
          name: 'mem',
        },
        {
          label: 'GPU Num',
          field: 'gpunum',
          name: 'gpunum',
        },
        {
          label: 'GPU Temp',
          field: 'gputemp',
          name: 'gputemp',
        },
        {
          label: 'GPU Power',
          field: 'gpupower',
          name: 'gpupower',
        },
        {
          label: 'Last Report Time',
          field: 'time',
          name: 'time',
        },
      ],
      rows: [
      ],
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main-table {
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
