<template>
  <q-page class="flex flex-center">
    <q-card style="width: 100vw; min-height: 800px">
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
      <q-card-section>
        <q-btn color="primary" @click="formatingtext(text)" class="full-width"
          >Crear JSON</q-btn
        >
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
  name: "PageFormatingText",
  components: {},
  setup() {
    const text = ref("");
    const text_formated = ref("");
    const showpopup = ref(false);

    const formatingtext = (text) => {
      try {
        // Split the input text by the special character ###
        let articles = text
          .split("###")
          .filter((article) => article.trim() !== "");

        // Function to extract content between tags
        function extractContent(article, tag) {
          const regex = new RegExp(`<${tag}>([\\s\\S]*?)<\\/${tag}>`, "i");
          const match = article.match(regex);
          return match ? match[1].trim() : "";
        }

        // Parse each article and create the structured object
        let result = articles.map((article) => {
          const title = extractContent(article, "title");
          const content = extractContent(article, "content");

          // Split content into paragraphs
          const paragraphs = content
            .split("\n\n")
            .map((p) => p.trim())
            .filter((p) => p !== "");

          return {
            title: title,
            text: paragraphs,
            category: [""],
            stringcategory: "",
            urlfile: "",
          };
        });

        text_formated.value = result;
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
    };
  },
});
</script>

<style scoped></style>
