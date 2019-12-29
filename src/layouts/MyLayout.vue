<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <div class="q-px-md q-pt-md q-mb-md">
      <a href="/" style="color: white; text-decoration: none;">
        <div class="text-h3">Blåljus</div>
        <div class="text-subtitle1">Rapporter från din lokala station</div>
        </a>
      </div>
      <q-img
        src="https://nyhetsfotograf.se/public_pictures/131012_polisbil/0.JPG"
        class="header-image absolute-top" />
    </q-header>
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
export default {
  name: 'MyLayout',
  data () {
    return {
      leftDrawerOpen: false
    }
  },
  methods: {
  }
}
</script>

<style>
  .header-image {
    height: 100%;
    z-index: -1;
    opacity: 0.4;
  }
</style>
