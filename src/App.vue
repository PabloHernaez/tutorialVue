<script>
// Importacion del componente "TablaPersonas"
import TablaPersonas from "@/components/TablaPersonas.vue";
import { onMounted, ref } from "vue";
import FormularioPersona from "./components/FormularioPersona.vue";

import { useCounterStore } from "@/stores/counter";
// Definicion del componente principal
export default {
  // Nombre del componente principal
  name: "App",
  // Registro de componentes utilizados en este componente principal
  components: {
    TablaPersonas,
    FormularioPersona,
  },

  setup() {
    const personas = ref([]);
    const store = useCounterStore();

    const url = import.meta.env.VITE_DJANGOURL;

    const listadoPersonas = async () => {
      try {
        const response = await fetch(url + "api/v1/personas/");
        var value = await response.json();
        personas.value = value;
      } catch (error) {
        console.error(error);
      }
    };

    const actualizarPersona = async (id, personaActualizada) => {
      try {
        const response = await fetch(
          url + "api/v1/personas/" + personaActualizada.id + "/",
          {
            method: "PUT",
            body: JSON.stringify(personaActualizada),
            headers: { "Content-type": "application/json; charset=UTF-8" },
          }
        );
        const personaActualizadaJS = await response.json();
        personas.value = personas.value.map((u) =>
          u.id === personaActualizada.id ? personaActualizadaJS : u
        );
      } catch (error) {
        console.error(error);
      }
    };

    const agregarPersona = async (persona) => {
      try {
        var json = JSON.stringify(persona);

        const response = await fetch(url + "api/v1/personas/", {
          method: "POST",
          body: JSON.stringify(persona),
          headers: { "Content-type": "application/json; charset=UTF-8" },
        });
        const personaCreada = await response.json();
        personas.value = [...personas.value, personaCreada];
        store.increment();
      } catch (error) {
        console.error(error);
      }
    };

    const eliminarPersona = async (id) => {
      try {
        await fetch(url + "api/v1/personas/" + persona_id + "/", {
          method: "DELETE",
        });
        personas.value = personas.value.filter((u) => u.id !== persona_id);
      } catch (error) {
        console.error(error);
      }
    };

    onMounted(() => {
      listadoPersonas();
    });
    return {
      personas,
      agregarPersona,
      eliminarPersona,
      actualizarPersona,
      store,
    };
  },
};
</script>

<template>
  <div id="app" class="container">
    <p>Count is {{ store.count }}</p>
    <div class="row">
      <div class="col-md-12">
        <h1>Personas</h1>
      </div>
    </div>
    <div class="row">
      <div class="col-md-12">
        <formulario-persona @add-persona="agregarPersona" />
        <tabla-personas
          :personas="personas"
          @delete-persona="eliminarPersona"
          @actualizar-persona="actualizarPersona" />
      </div>
    </div>
  </div>
</template>

<style>
/* Estilos globales para todos los elementos button en la aplicacion
,→ */
button {
  background: #009435;
  border: 1px solid #009435;
}
</style>
