<template>
  <q-page class="flex flex-center">
    <q-card>
      <q-card-section>
        <div class="row justify-center">
          <p style="font-size: 30pt">Login</p>
          <div class="col-12"><q-input outlined label="Email" /></div>
          <div class="col-12 q-mt-sm">
            <q-input outlined label="Senha" />
          </div>
        </div>
      </q-card-section>
      <q-card-section class="row justify-center items-center">
        <router-link to="/home">
          <q-btn label="Entrar" color="blue" />
        </router-link>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'IndexPage',
});
</script>
