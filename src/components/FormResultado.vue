<template>
  <v-card>
    <v-card-title>
      <span class="text-h5">Agregar Resultado</span>
    </v-card-title>
    <v-card-text>
      <v-container>
        <v-form ref="form" lazy-validation>
          <v-row class="form-calendar">
            <v-col cols="12" md="4">
              <v-text-field :rules="txtRules" label="Resultado" v-model="editedItem.resultado" required dense outlined></v-text-field>
            </v-col>
            <v-col cols="12" md="4">
              <v-text-field :rules="numberRules" label="Exitosos" v-model="editedItem.exito" required dense outlined></v-text-field>
            </v-col>
            <v-col cols="12" md="4">
              <v-text-field :rules="numberRules" label="Fallidos" v-model="editedItem.fallidos" required dense outlined></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" md="4">
              <v-text-field :rules="numberFloatRules" label="Isr Timbrado" v-model="editedItem.isrTimbrado" dense required outlined></v-text-field>
            </v-col>
            <v-col cols="12" md="4">
              <v-text-field :rules="txtRules" label="Descarga(URL)" v-model="editedItem.urlDescarga" dense required outlined></v-text-field>
            </v-col>
            <v-col cols="12" md="4">
              <v-text-field :rules="txtRules" label="PDF" v-model="editedItem.pdf" dense required outlined></v-text-field>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12" md="4">
              <v-text-field :rules="txtRules" label="QR" v-model="editedItem.qr" dense required outlined></v-text-field>
            </v-col>
            <v-col cols="12" md="4">
              <v-text-field :rules="txtRules" label="XML" v-model="editedItem.xml" dense required outlined></v-text-field>
            </v-col>
          </v-row>
          <div>
            <v-menu v-model="menu" :close-on-content-click="false" :nudge-right="40" transition="scale-transition"
              offset-y min-width="auto">
              <template v-slot:activator="{ on, attrs }">
                <v-text-field :rules="txtRules" v-model="dateFechaFin" label="Fecha Fin" prepend-icon="mdi-calendar" readonly required
                  v-bind="attrs" v-on="on"></v-text-field>
              </template>
              <v-date-picker v-model="dateFechaFin" @input="menu = false" no-title scrollable></v-date-picker>
            </v-menu>
          </div>
          <v-row>
            <v-col cols="12">
              <v-textarea :rules="txtRules" label="Observaciones" v-model="editedItem.observaciones" name="input-7-4" outlined></v-textarea>
            </v-col>
          </v-row>
        </v-form>
      </v-container>
    </v-card-text>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-btn color="error darken-1" text @click="closeResultado"> Cancelar </v-btn>
      <v-btn color="blue darken-1" text @click="saveData"> Guardar </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
import axios from "axios";

export default {
  name: "Resultado",
  components: {
    // Calendario,
  },
  props: {
    idResultado: "",
    timbradoProp: true,
    resultadoProp: true,
    idTimbrado: "",
  },
  data: () => ({
    txtRules: [
      (v) => !!v || "Este campo es requerido",
    ],
    numberFloatRules: [
      (v) => !!v || "Este campo es requerido",
      v => /^[0-9]+([.][0-9]+)?$/.test(v) || 'Valores entre 0-9',
    ],
    numberRules: [
      (v) => !!v || "Este campo es requerido",
      v => /^[0-9]+$/.test(v) || 'Solo números enteros',
    ],
    concepto: "",
    dialog: false,
    search: "",
    status: true,
    result: [],
    menu: false,
    dateFechaFin: "",
    desserts: [],
    editedItem: [
      {
        resultado: "",
        exito: "",
        fallidos: "",
        isrTimbrado: "",
        urlDescarga: "",
        pdf: "",
        qr: "",
        xml: "",
        observaciones: "",
      },
    ],
  }),
  created() {
    this.getMapping();
  },
  computed: {
    computedDateFormatted() {
      return this.formatDate(this.date);
    },
    dateRangeText() {
      return this.dates.join(" ~ ");
    },
  },
  methods: {
    getMapping() {
      if (this.idResultado !== undefined) {
        try {
          axios.get("http://localhost:8082/Finalizado/" + this.idResultado).then((response) => {
            this.editedItem.resultado = response.data.resultado;
            this.editedItem.exito = response.data.exito;
            this.editedItem.fallidos = response.data.fallidos;
            this.editedItem.isrTimbrado = response.data.isrTimbrado;
            this.editedItem.urlDescarga = response.data.urlDescarga;
            this.editedItem.pdf = response.data.pdf;
            this.editedItem.qr = response.data.qr;
            this.editedItem.xml = response.data.xml;
            this.dateFechaFin = response.data.fechaFinalizado;
            this.editedItem.observaciones = response.data.observaciones;
          })
        } catch (error) {

        }
      }
    },
    saveData: function () {
      if (this.timbradoProp) {
        axios
          .post("http://localhost:8082/Finalizado", {
            resultado: this.editedItem.resultado,
            exito: this.editedItem.exito,
            fallidos: this.editedItem.fallidos,
            isrTimbrado: this.editedItem.isrTimbrado,
            urlDescarga: this.editedItem.urlDescarga,
            pdf: this.editedItem.pdf,
            qr: this.editedItem.qr,
            xml: this.editedItem.xml,
            fechaFinalizado: this.dateFechaFin,
            observaciones: this.editedItem.observaciones,
            timbradoEntity: { id: this.idTimbrado },
            status: this.status,
          })
          .then(() => {
            this.$emit("closeCompRes")
          });
      }
      if(this.resultadoProp){
        axios
          .put("http://localhost:8082/Finalizado/" + this.idResultado, {
            resultado: this.editedItem.resultado,
            exito: this.editedItem.exito,
            fallidos: this.editedItem.fallidos,
            isrTimbrado: this.editedItem.isrTimbrado,
            urlDescarga: this.editedItem.urlDescarga,
            pdf: this.editedItem.pdf,
            qr: this.editedItem.qr,
            xml: this.editedItem.xml,
            fechaFinalizado: this.dateFechaFin,
            observaciones: this.editedItem.observaciones,
            timbradoEntity: { id: this.idTimbrado },
            status: this.status,
          })
          .then(() => {
            this.$emit("closeCompRes")
          });
      }
    },
    closeResultado() {
      this.$emit("closeCompRes")
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
    onlyNumber($event) {
      let keyCode = $event.keyCode ? $event.keyCode : $event.which;
      if ((keyCode < 48 || keyCode > 57) && keyCode !== 46) {
        // 46 is dot
        $event.preventDefault();
      }
    },
    formatDate(date) {
      if (!date) return null;

      const [year, month, day] = date.split("-");
      return `${month}/${day}/${year}`;
    },
    parseDate(date) {
      if (!date) return null;

      const [month, day, year] = date.split("/");
      return `${year}-${month.padStart(2, "0")}-${day.padStart(2, "0")}`;
    },
  },
};
</script>
