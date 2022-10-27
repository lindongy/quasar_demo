<template>
  <q-layout view="hHh Lpr lff">
    <q-header elevated
    >
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>
          Yoctopuce Quasar test
        </q-toolbar-title>
      </q-toolbar>
    </q-header>
    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      :width="200"
      :breakpoint="400"
      bordered
      elevated
      class="bg-grey-3"
    >
      <q-scroll-area
        class="fit"
      >
        <q-list padding class="menu-list">
          <q-item
            to="/"
            exact
            clickable
            v-ripple>
            <q-item-section avatar>
              <q-icon name="list" />
            </q-item-section>

            <q-item-section>
              Devices
            </q-item-section>
          </q-item>
          <q-item
            to="/about"
            exact
            clickable v-ripple>
            <q-item-section avatar>
              <q-icon name="info" />
            </q-item-section>
            <q-item-section>
              About
            </q-item-section>
          </q-item>
          <q-separator  />
          <q-item
            to="/settings"
            exact
            clickable v-ripple>
            <q-item-section avatar>
              <q-icon name="settings" />
            </q-item-section>
            <q-item-section>
              Settings
            </q-item-section>
          </q-item>
        </q-list>
      </q-scroll-area>
    </q-drawer>

    <q-page-container>
      <keep-alive>
        <router-view />
      </keep-alive>
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { YAPI } from 'yoctolib-esm/yocto_api';

export default defineComponent({
  name: 'MainLayout',

  data()
  {
    return {
      leftDrawerOpen: false
    };
  },
  computed: {
    yapi_version()
    {
      return YAPI.imm_GetAPIVersion();
    }
  },
  methods: {
    toggleLeftDrawer()
    {
      this.leftDrawerOpen = !this.leftDrawerOpen;
    }
  }
});
</script>
<style lang="sass" scoped>
.menu-list .q-item
  border-radius: 0 32px 32px 0
</style>
