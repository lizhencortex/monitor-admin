<template>
  <q-layout ref="layout" view="hHr LpR lFf">
    <q-layout-header v-once>
      <q-toolbar>
        <q-icon name="graphic_eq" color="cyan-4" />
        <q-toolbar-title>
          Server Monitor
          <span slot="subtitle">Cortex Node Monitor</span>
        </q-toolbar-title>
      </q-toolbar>
    </q-layout-header>

    <q-page-container>
      <router-view/>
    </q-page-container>

    <q-btn round v-once color="cyan-5"
           class="fixed-bottom-right q-mr-md q-mb-md">
      <q-icon name="keyboard_arrow_up" />
    </q-btn>

  </q-layout>
</template>

<style scoped>
  .q-layout {
    background: linear-gradient(#656671, #464653);
  }
  .q-toolbar {
    background-color: #3e3d44 !important;
  }
</style>
