<template>
  <div class="row justify-center">
    <div class="col-11 q-mt-xl">
      <q-card>
        <q-card-section class="row justify-center">
          <q-card>
            <q-card-section class="row justify-center" color="grey">
              <p class="large">Liberado para uso ?</p>
              <q-icon v-if="distancia > 10" name="done" color="green" size="xl" />
              <q-icon v-else name="close" color="red" size="xl" />
            </q-card-section>

            <q-card-section class="justify-center" color="grey">
              <div>
                <apexchart width="400" type="radialBar" :options="options" :series="humidade"></apexchart>
              </div>
              <div class="row justify-center">
                <p class="large">Humidade do reboque</p>
              </div>
            </q-card-section>
            <div class="q-mx-md" style="border-top: 1px solid grey"></div>
            <q-card-section class="row justify-center" color="grey">
              <p class="large">A temperatura interna do reboque é {{ tempUbidots }}º</p>
            </q-card-section>
            <q-card-section class="row justify-center" color="grey">
              <p>Data última atualização: {{ lastUpdate }}</p>
            </q-card-section>
          </q-card>
        </q-card-section>
      </q-card>
    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue';
import axios from 'axios';
import { useQuasar } from 'quasar';
import VueApexCharts from 'vue3-apexcharts';

export default defineComponent({
  name: 'HomePage',
  components: {
    apexchart: VueApexCharts,
  },
  setup() {
    const $q = useQuasar();
    return {
      showLoading() {
        $q.loading.show();
      },
      hideLoading() {
        $q.loading.hide();
      },
      showError(msg) {
        $q.notify({
          message: msg,
          type: 'negative',
          position: 'center',
        });
      },
    };
  },
  data() {
    return {
      ubidotsInfo: {},
      tempUbidots: '',
      humidade: [0],
      distancia: 0,
      timer: '',
      lastUpdate: '',
      options: {
        chart: {
          type: 'radialBar',
          offsetY: -20,
          sparkline: {
            enabled: true,
          },
        },
        plotOptions: {
          radialBar: {
            startAngle: -90,
            endAngle: 90,
            track: {
              background: '#e7e7e7',
              strokeWidth: '97%',
              margin: 5, // margin is in pixels
              dropShadow: {
                enabled: true,
                top: 2,
                left: 0,
                color: '#999',
                opacity: 1,
                blur: 2,
              },
            },
            dataLabels: {
              name: {
                show: false,
              },
              value: {
                offsetY: -2,
                fontSize: '22px',
              },
            },
          },
        },
        grid: {
          padding: {
            top: -10,
          },
        },
        fill: {
          type: 'gradient',
          gradient: {
            shade: 'light',
            shadeIntensity: 0.4,
            inverseColors: false,
            opacityFrom: 1,
            opacityTo: 1,
            stops: [0, 50, 53, 91],
          },
        },
        labels: ['Average Results'],
      },
      series: [
        {
          name: 'series-1',
          data: [75],
        },
      ],
    };
  },
  async created() {
    await this.getValuesUbidots();
    setInterval(async () => {
      try {
        this.showLoading();
        const res = await axios.get(
          'https://industrial.api.ubidots.com/api/v2.0/devices/6366d9684b577f000b689216/_/values/last',
          {
            headers: {
              'X-Auth-Token': 'BBFF-nbZ0mETzYYzdwYRBApltFnojx7Rg1D',
            },
          }
        );
        this.ubidotsInfo = res.data;
      } catch (error) {
        console.log(error);
        this.showError(error);
      } finally {
        this.hideLoading();
      }
    }, 5000);
  },
  beforeUnmount() {
    this.cancelAutoUpdate();
  },
  methods: {
    async getValuesUbidots() {
      try {
        this.showLoading();
        const res = await axios.get(
          'https://industrial.api.ubidots.com/api/v2.0/devices/6366d9684b577f000b689216/_/values/last',
          {
            headers: {
              'X-Auth-Token': 'BBFF-nbZ0mETzYYzdwYRBApltFnojx7Rg1D',
            },
          }
        );
        this.ubidotsInfo = res.data;
      } catch (error) {
        this.showError(error);
      } finally {
        this.hideLoading();
      }
    },
    cancelAutoUpdate() {
      clearInterval(this.timer);
    },
  },
  watch: {
    ubidotsInfo() {
      this.humidade = [this.ubidotsInfo.sensor_humidade.value];
      this.distancia = this.ubidotsInfo.sensor_distancia.value;
      this.tempUbidots = this.ubidotsInfo.sensor_temperatura.value;
      this.lastUpdate = new Date(this.ubidotsInfo.sensor_humidade.timestamp).toLocaleString();
    },
  },
});
</script>

<style lang="scss">
.large {
  font-size: x-large;
}
</style>
