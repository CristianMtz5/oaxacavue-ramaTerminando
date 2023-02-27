<template>
  <v-container>
    <v-data-table style="background-color: #6868A7" :headers="headers" :items="desserts" class="elevation-1">
      <template v-slot:[`item.actions`]="{ item }">
        <v-btn class="mr-3" elevation="1" color="primary" fab dark tile x-small @click="editItem(item.id)">
          <v-icon small> mdi-pencil </v-icon>
        </v-btn>
        <v-btn class="mr-3" elevation="1" color="error" fab dark tile x-small @click="ocultarFila(item.id)">
            <v-icon small> mdi-delete </v-icon>
          </v-btn>
      </template>
    </v-data-table>
    <v-dialog v-model="dialogResultado" max-width="700px">
      <FormResultado resultadoProp="resultadoProp" @closeCompRes="close" :idResultado="idResultado" />
    </v-dialog>
  </v-container>
</template>

<script>
import axios from 'axios';
import FormResultado from './FormResultado.vue';

export default{
  name: "ItemsResultado",
  components:{
    FormResultado,
  },
  props:{
    idTimbradoHa: "",
  },
  data: () =>({
    idResultado: "",
    resultadoProp: "resultadoProp",
    dialogResultado: false,
    desserts: [],
    headers: [
      { text: "ID", align:"start", value:"id" },
      { text: "Resultado", value:"resultado" },
      { text: "Exitosos", value:"exito" },
      { text: "Fallidos", value:"fallidos" },
      { text: "Isr Timbrado", value:"isrTimbrado" },
      { text: "Descarga (URL)", value:"urlDescarga" },
      { text: "PDF", value:"pdf" },
      { text: "QR", value:"qr" },
      { text: "XML", value:"xml" },
      { text: "Fecha Fin", value:"fechaFinalizado" },
      { text: "Observaciones", value:"observaciones" },
      { text: "Opciones", value:"actions" }
    ],
  }),
  created(){
    this.getMapping();
  },
  methods: {
    getMapping() {
      this.desserts.length = "";
      axios.get("http://localhost:8082/Finalizado/dataFinalizado/" + this.idTimbradoHa + "/true").then((response) => {
        this.result = response.data.data;
        for (let i = 0; i < response.data.length; i++) {
          this.desserts.push({
            id: response.data[i].id,
            resultado: response.data[i].resultado,
            exito: response.data[i].exito,
            fallidos: response.data[i].fallidos,
            isrTimbrado: response.data[i].isrTimbrado,
            urlDescarga: response.data[i].urlDescarga,
            pdf: response.data[i].pdf,
            qr: response.data[i].qr,
            xml: response.data[i].xml,
            fechaFinalizado: response.data[i].fechaFinalizado,
            observaciones: response.data[i].observaciones,
            idTimbrado: response.data[i].timbradoEntity.idTimbrado,
          });
        }
      });
    },
    editItem(id){
      this.idResultado = id;
      this.dialogResultado = true;
    },
    ocultarFila(id){
      let statusFalse = false;
      axios.put("http://localhost:8082/Finalizado/statusFinalizado/" + id, {
        status : statusFalse,
      }).then(()=>{
        this.getMapping();
      })
    },
    close() {
      this.dialog = false;
      this.dialogResultado = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
  }
}
</script>