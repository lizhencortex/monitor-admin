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
        this.rows = []
        for (let x of data) {
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
                ip: x.ip,
                lip: x.sysinfo.localip.replace(/inet addr\:/g, '').replace(/inet/g, '').replace(/addr:/g, ''),
                location: `${x.ipgeo.countryCode}, ${x.ipgeo.region}, ${x.ipgeo.city}`,
                mac: x.mac,
                cpu: cpuinfo,
                blocknum: x.sysinfo.blocknum,
                totalMem: mem[0],
                usedMem: mem[1],
                availMem: mem[5],
                gpunum: x.gpuinfo['Attached GPUs'],
                gputemp: gputemp,
                gpupower: gpupower,
                gpufan: gpufan,
                mem: `${mem[1]} / ${mem[0]}`,
                time: t.toDateString().slice(4) + ", " + t.toTimeString().slice(0, 8),
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
