<template>
  <div>
    <v-layout :wrap="true">
      <v-flex xs12>
        <v-card>
          <v-row justify="center">
            <v-date-picker
              v-model="picker"
              full-width
              locale="es-mx"
              :min="minimo"
              :max="maximo"
              @change="getDolar(picker)">
            </v-date-picker>
          </v-row>
        </v-card>
        <v-card color="error" dark>
          <v-card-text class="display-1 text-xl-center">
            {{ valor }} - {{ picker }}
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
import axios from 'axios'
import { mapMutations } from 'vuex'

export default {
  data () {
    return {
      picker: new Date().toISOString().substr(0, 10),
      minimo: '1984',
      maximo: new Date().toISOString().substr(0, 10),
      valor: null
    }
  },
  methods: {
    ...mapMutations(['mostrarLoading', 'ocultarLoading']),
    async getDolar (dia) {
      // console.log(dia)
      let arrayFecha = dia.split('-')
      console.log(arrayFecha)
      let ddmmyyy = arrayFecha[2] + '-' + arrayFecha[1] + '-' + arrayFecha[0]
      // console.log(ddmmyyy)
      try {
        this.mostrarLoading({ titulo: 'Accediendo a información', color: 'secondary' })
        let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyyy}`)
        if (datos.data.serie.length > 0) {
          this.valor = await datos.data.serie[0].valor
        } else {
          this.valor = 'Sin resultados'
        }
        // console.log(datos.data.serie[0].valor)
      } catch (error) {
        console.log(error)
      } finally {
        this.ocultarLoading()
      }
    }
  },
  created () {
    this.getDolar(new Date().toISOString().substr(0, 10))
    /* axios.get('https://mindicador.cl/api/dolar')
      .then(response => {
        this.dolar = response.data
        console.log(this.dolar)
      })
      .catch(e => {
        this.errors.push(e)
      }) */
  }
}
</script>
