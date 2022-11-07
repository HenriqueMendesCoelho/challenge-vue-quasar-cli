<template>
  <router-view />
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "App",
});
</script>

<style lang="scss">
body {
  background-color: grey;
}

::-webkit-scrollbar {
  width: 0.5rem;
}

::-webkit-scrollbar-track {
  background-color: var(--grey-mid);
}

::-webkit-scrollbar-thumb {
  background-color: var(--light-grey2);
  border-radius: 10px;
}

::-webkit-scrollbar-thumb:hover {
  background-color: var(--light-grey);
}
</style>
