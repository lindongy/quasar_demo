<template>
  <q-page class="column q-mx-lg">
    <h3>Settings</h3>
    <p>
      By default this application try to connect to the local VirtualHub (127.0.0.1).
      If you want to connect to a YoctoHub or a remote VirtualHub you can change the URL used by the application.
    </p>
    <q-input v-model="url" label="URL" class="q-px-md q-my-lg">
      <template v-slot:append>
        <q-btn round flat icon="save "
               @click="update_url"
        />
      </template>
    </q-input>

  </q-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { LocalStorage, Notify } from 'quasar';

export default defineComponent({
  name: 'IndexPage',

  data()
  {
    return {
      url: '127.0.0.1',
    };
  },
  methods: {
    async update_url()
    {
      LocalStorage.set('hub_url', this.url);
      Notify.create('New url is saved.')
    }
  },
  async created()
  {
    let url = LocalStorage.getItem('hub_url');
    if (url) {
      this.url = url as string;
      console.log('use value for local storage', url);

    }
  }
});
</script>
