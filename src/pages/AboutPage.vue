<template>
  <q-page class="column q-ml-lg">
    <h5>About</h5>
    <p>This is a small demo application that use <a href="https://quasar.dev/">Qasar framework</a> and <a href="https://www.yoctopuce.com/EN/libraries.php">Yoctopuce TypeScript library</a>. You can use it as starting point but be aware that it's not intended to be use in production. </p>
    <p>You can find more information on this application our web site <a href="https://www.yoctopuce.com">yoctopuce.com</a> or on GitHub : <a href="https://github.com/yoctopuce-examples/quasar_demo">https://github.com/yoctopuce-examples/quasar_demo</a></p>
  </q-page>
</template>

<script lang="ts">

import { defineComponent } from 'vue';

export default defineComponent({
  name: 'AboutPage',
});
</script>
