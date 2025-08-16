<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="head-c">
      <q-toolbar>
        <img src="~assets/cigarette.png" alt="" style="width:4.5rem;">

        <q-toolbar-title> Smoker App </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
</script>

<style lang="scss">
.head-c{
  background-color: $cigarette;
}
</style>
