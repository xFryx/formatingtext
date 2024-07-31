<template>
  <q-page class="flex flex-center">
    <q-card style="width: 100vw;">
      <q-card-section>
        <div class="row">
          <div class="col-12">
            <q-input
              type="textarea"
              label="Coloca el texto formateado"
              outlined
              rows="20"
              v-model="text"
            >
            </q-input>
          </div>
        </div>
      </q-card-section>
    </q-card>
    <q-card style="width: 100vw; min-height: 800px">
      <q-card-section v-for="(item,index) in arrayDates" :key="index">
        <q-card class="q-ma-md q-pa-md">
          <div class="row" >
          <div class="col-12">
            <q-input
              label="Coloca la fecha"
              outlined
              v-model="item.date"
            >
            </q-input>
          </div>
        </div>
        <div class="row q-mt-md q-col-gutter-md">
          <div class="col-12 col-sm-6">
            <q-input

              label="Coloca el primer articulo del mes"
              outlined
              type="textarea"
              rows="3"
              v-model="item.startIndex"
            >
            </q-input>
          </div>
          <div class="col-12 col-sm-6">
            <q-input
              label="Coloca el ultimo articulo del mes"
              outlined
              v-model="item.finishIndex"
              type="textarea"
              rows="3"
            >
            </q-input>
          </div>
        </div>
        </q-card>
      </q-card-section>
      <q-card-section>

      </q-card-section>
      <q-card-section>
        <div class="row q-col-gutter-md">
          <div class="col-12 col-sm-6">
            <q-btn color="primary" @click="add_date()" class="full-width"
          >Agregar fecha</q-btn
        >
          </div>
          <div class="col-12 col-sm-6">
            <q-btn color="primary" @click="formatingtext()" class="full-width"
          >Crear JSON con fechas</q-btn
        >
          </div>
        </div>

      </q-card-section>
    </q-card>
    <q-dialog
      v-model="showpopup"
      :maximized="true"
      transition-show="slide-up"
      transition-hide="slide-down"
    >
      >

      <q-card>
        <q-toolbar class="bg-primary">
          <q-toolbar-title
            ><span class="text-weight-bold text-white">
              Texto formateado
            </span></q-toolbar-title
          >

          <q-btn color="white" flat round dense icon="close" v-close-popup />
        </q-toolbar>
        <q-card-section style="text-align: justify" class="q-pt-none">
          <q-card class="q-pa-md q-ma-md">
            <q-card-section>
              {{ text_formated }}
            </q-card-section>
          </q-card>
        </q-card-section>

        <q-card-actions align="right">
          <q-btn
            class="full-width"
            color="primary"
            label="CERRAR"
            v-close-popup
          />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";

export default defineComponent({
  name: "DatePage",
  components: {},
  setup() {
    const text = ref("");
    const text_formated = ref("");
    const showpopup = ref(false);

    const arrayDates = ref([ {
        startIndex: "",
        finishIndex: "",
        date: ""
      }
    ])



    const add_date = () => {
      arrayDates.value.push({
      startIndex: "",
      finishIndex: "",
      date: ""
    })
    }

    const get_index = (indexToSearch,articles) => {

      for (let index = 0; index < articles.length; index++) {
        if( articles[index].title.trim() == indexToSearch.trim()  ){
          return index;
        }

      }
      return -1;
    }

    const formatingtext = () => {

      try {
        // Split the input text by the special character ###
        const articles = JSON.parse(text.value);
       for (let index = 0; index < arrayDates.value.length; index++) {

          const startIndex = get_index(arrayDates.value[index].startIndex, articles);
          const finishIndex = get_index(arrayDates.value[index].finishIndex, articles);

          console.log(startIndex);
          console.log(finishIndex);

          if(startIndex != -1 && finishIndex != -1){
            for (let index2 = startIndex; index2 < finishIndex; index2++) {
              articles[index2]  = {
                ...articles[index2],
                date: arrayDates.value[index].date
              }
            }
          }else{

            text_formated.value = "ERROR EL ARTICULO INICIAL: "+arrayDates.value[index].startIndex+" | FINAL: "+arrayDates.value[index].finishIndex+" NO SE HAN ENCONTRADO EN EL JSON. ASEGURATE DE QUE NO TENGAN ESPACIO EN BLANCO O ESTÉN BIEN ESCRITOS";
            showpopup.value = true;
            return 0
          }
       }

        text_formated.value = articles;
        showpopup.value = true;
        //return result;
      } catch (error) {
        console.log(error);
      }
    };

    onMounted(() => {
      //height.value = window.innerHeight;
      //steps_in_form.set_steps_in_component("demand");
    });
    return {
      text,
      formatingtext,
      text_formated,
      showpopup,
      arrayDates,
      add_date,
    };
  },
});
</script>

<style scoped></style>
