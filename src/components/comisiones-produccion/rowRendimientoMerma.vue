<template>
  <tr>
    <td style="width:20%" class="text-justify">{{variable.descripcion}}</td>
    <td style="width:10%" class="center" v-for="day in days" :key="day">{{getRendimiento(tipo, day, variable.id) | number}}</td>
    <td style="width:10%" class="center disabled">{{getRendimientoTotal(tipo, variable.id) | number}}</td>
  </tr>
</template>
<script>
export default {
  props:['tipo', 'variable','fecha', 'days', 'rendimientoMerma'],
  created() {
  },
  data() {
    return {};
  },
  filters: {
    number(value) {
      if (!value) return 0;
      return new Intl.NumberFormat("en-IN").format(value);
    }
  },
  methods: {
    getRendimientoVariable(variable) {
      if (variable.descripcion == 'Carro Tanque') {
          return variable.valor_inicial;
      }
      return variable.valor_final - variable.valor_inicial;
    },
    getRendimiento(tipo, day, variable_id) {
      const date = getDate(this.fecha, this.days, String(day));
      const key = Object.keys(this.rendimientoMerma).find(d => d.substr(1) === date);
      const variables = this.rendimientoMerma[key].tipos[tipo];
      const variable = variables.find( v => v.id == variable_id);
      return variable ? this.getRendimientoVariable(variable) : 0;
    },
    getRendimientoTotal(tipo, variable_id) {
      return Object.values(this.rendimientoMerma).reduce( (total,fecha) => {
        const key = Object.keys(fecha.tipos).find( t => t === tipo);
        const variable = fecha.tipos[key].find(v => v.id == variable_id);
        return total + Number(variable ? this.getRendimientoVariable(variable) : 0);
      }, 0)
    },
  },
  computed: {
    tiposKeys() {
      return Object.keys(this.tipos);
    }
  },
}
</script>
<style lang="scss">
  .bold { 
    font-weight: bold;
  }
  tr.center {
    td {
      text-align: center;;
    }
    .left-align {
      text-align: left;
    }
  }
  .bb-0 {
    border-bottom: none !important;
  }
  .bg-gray {
    background: #F4F4F4;
  }
</style>
