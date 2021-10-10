<template>
  <q-layout view="lHh Lpr lFf" class="layout">
    <q-header style="height:80px">
      <q-toolbar>
        <!-- <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="leftDrawerOpen = !leftDrawerOpen"
        /> -->

        <q-toolbar-title>
          Dan is pro online editor for beginner programmers to get into dsa 😤👌
        </q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <!-- <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
      content-class="bg-grey-1"
    >
      <q-list>
        <q-item-label
          header
          class="text-grey-8"
        >
          Essential Links
        </q-item-label>
        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer> -->

    <q-page-container class="page-container">
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
{
  // import EssentialLink from 'components/EssentialLink.vue'
  // const linksData = [
  //   {
  //     title: 'Docs',
  //     caption: 'quasar.dev',
  //     icon: 'school',
  //     link: 'https://quasar.dev'
  //   },
  //   {
  //     title: 'Github',
  //     caption: 'github.com/quasarframework',
  //     icon: 'code',
  //     link: 'https://github.com/quasarframework'
  //   },
  //   {
  //     title: 'Discord Chat Channel',
  //     caption: 'chat.quasar.dev',
  //     icon: 'chat',
  //     link: 'https://chat.quasar.dev'
  //   },
  //   {
  //     title: 'Forum',
  //     caption: 'forum.quasar.dev',
  //     icon: 'record_voice_over',
  //     link: 'https://forum.quasar.dev'
  //   },
  //   {
  //     title: 'Twitter',
  //     caption: '@quasarframework',
  //     icon: 'rss_feed',
  //     link: 'https://twitter.quasar.dev'
  //   },
  //   {
  //     title: 'Facebook',
  //     caption: '@QuasarFramework',
  //     icon: 'public',
  //     link: 'https://facebook.quasar.dev'
  //   },
  //   {
  //     title: 'Quasar Awesome',
  //     caption: 'Community Quasar projects',
  //     icon: 'favorite',
  //     link: 'https://awesome.quasar.dev'
  //   }
  // ];
}

export default {
  name: "MainLayout",
  components: {},
  data() {
    return {
      leftDrawerOpen: false
    };
  }
};
</script>
<style scoped>
.layout {
  background: #1976d263;
  backdrop-filter: blur(10px);
  background-image: url("../assets/animated-bg.svg");
  background-repeat: no-repeat;
  background-size: 1920px 100vh;
}
.page-container {
}
</style>
