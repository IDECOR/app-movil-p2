<template>
  <q-layout view="lHh Lpr lFf">
    <q-header class="bg-grey-10 text-grey-4">
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
          <img     alt="IDECOR"
            src="https://idecor.cba.gov.ar/wp-content/uploads/2017/07/cropped-logo-IDECOR.png"
            width="120px"
          >
        </q-toolbar-title>


      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>

export default {
  name: 'MainLayout',
  components: {
  },
  data () {
    return {
    }
  }
}
</script>
