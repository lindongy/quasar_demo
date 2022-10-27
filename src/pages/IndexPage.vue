<template>
  <q-page class="column">
    <h5
      class=" q-ml-lg"
    >Device list</h5>
    <p class=" q-mx-lg">
      Here is the list of all Yoctopuce devices connected to {{ url }}. Each beacon button will toggle the blue beacon
      led on matching device allowing you to locate it.
    </p>
    <q-list bordered padding class="rounded-borders">
      <!--
      <q-item>
        <q-item-section
          side
        >
          <q-icon name="online_prediction"
          />
        </q-item-section>
        <q-item-section>
          <q-item-label>Serial</q-item-label>
        </q-item-section>
        <q-item-section>
          <q-item-label>Logical Name</q-item-label>
        </q-item-section>
        <q-item-section>
          <q-item-label>Description</q-item-label>
        </q-item-section>
        <q-item-section>
          <q-item-label>Action</q-item-label>
        </q-item-section>
      </q-item>
-->
      <q-item
        v-for="item in module_list"
        :key="item.serial"
        v-ripple>
        <q-item-section
          side>
          <q-icon name="online_prediction"
                  :class="{ 'text-blue': item.beacon }"
          />
        </q-item-section>
        <q-item-section>
          <q-item-label>{{ item.serial }}</q-item-label>
        </q-item-section>
        <q-item-section>
          <q-item-label>{{ item.name }}</q-item-label>
        </q-item-section>
        <q-item-section>
          <q-item-label>{{ item.product }}</q-item-label>
        </q-item-section>
        <q-item-section>
          <q-btn
            v-if="item.product!=='VirtualHub'"
            rounded
            @click="toogle_beacon(item.serial, !item.beacon)"
            color="primary"
            size="xs"
            label="beacon"
          />
        </q-item-section>
      </q-item>
    </q-list>
  </q-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { YAPI, YErrorMsg, YModule } from 'yoctolib-esm/yocto_api_html.js';
import { Dialog, Loading, LocalStorage } from 'quasar';

interface ModuleItem
{
  serial: string;
  name: string;
  product: string;
  beacon: boolean;
}

export default defineComponent({
  name: 'IndexPage',

  data()
  {
    return {
      url: '127.0.0.1',
      timeout_ref: 0 as number,
      yapi_error: '' as string,
      module_list: [] as ModuleItem[]
    };
  },
  methods: {
    async toogle_beacon(serial: string, beacon: boolean)
    {
      let yModule: YModule = YModule.FindModule(serial);
      let newval = beacon ? YModule.BEACON_ON : YModule.BEACON_OFF;
      await yModule.set_beacon(newval);
      await this.reloadState();
    },
    async reloadState()
    {
      let errmsg = new YErrorMsg();
      await YAPI.UpdateDeviceList(errmsg);
      this.module_list = [];
      let module = YModule.FirstModule();
      while (module) {
        let beacon = await module.get_beacon();
        let m: ModuleItem = {
          serial: await module.get_serialNumber(),
          product: await module.get_productName(),
          name: await module.get_logicalName(),
          beacon: beacon == YModule.BEACON_ON
        };
        this.module_list.push(m);
        module = await module.nextModule();
      }
      this.timeout_ref = window.setTimeout(this.reloadState, 500);
    },
    async update_url()
    {
      LocalStorage.set('hub_url', this.url);
      console.log('updated');
    },
    fatalError(error: string)
    {
      Dialog.create({
        title: 'Fatal error',
        message: error
      }).onOk(() => {
        // console.log('OK')
      }).onCancel(() => {
        // console.log('Cancel')
      }).onDismiss(() => {
        // console.log('I am triggered on both OK and Cancel')
      });
    }


  },
  async created()
  {
    let url = LocalStorage.getItem('hub_url');
    if (url) {
      this.url = url as string;
      console.log('use value for local storage', url);
    }
    Loading.show({
      message: 'Try to connect to ' + this.url
    });

    try {
      await YAPI.LogUnhandledPromiseRejections();
      let errmsg: YErrorMsg = new YErrorMsg;
      await YAPI.RegisterHub(this.url, errmsg);
      await this.reloadState();
    } catch (e) {
      console.log(e);
      Loading.hide();
      this.fatalError('RegisterHub failed with "' + e + '"');
    }
    Loading.hide();
    await this.reloadState();
  },
  beforeUnmount()
  {
    console.log('unmount');
    if (this.timeout_ref) {
      window.clearTimeout(this.timeout_ref);
      this.timeout_ref = 0;
    }
    YAPI.UnregisterHub(this.url);
  }

});
</script>

<style lang="scss">
.yocto_icon {
  width: 40px;
  height: 40px;
}

.yocto_icon_grey {
  filter: grayscale(100%);
}
</style>
