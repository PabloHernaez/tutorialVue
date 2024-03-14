<script>
import { ref } from "vue";

export default {
  // Nombre del componente
  name: "TablaPersonas",

  props: {
    // La propiedad 'personas' se espera que sea un array
    personas: {
      type: Array,
      default: () => [],
    },
  },

  emits: ["actualizar-persona", "delete-persona"],

  setup(props, ctx) {
    const editando = ref(null);
    const personaEditada = ref(null);

    const guardarPersona = (persona) => {
      if (
        !persona.nombre.length ||
        !persona.apellido.length ||
        !persona.email.length
      ) {
        return;
      }
      ctx.emit("actualizar-persona", persona.id, persona);
      editando.value = null;
    };

    const cancelarEdicion = (persona) => {
      Object.assign(persona, personaEditada.value);
      editando.value = null;
    };

    const editarPersona = (persona) => {
      personaEditada.value = { ...persona };
      editando.value = persona.id;
    };

    return {
      editando,
      editarPersona,
      guardarPersona,
      cancelarEdicion,
    };
  },
};
</script>

<template>
  <!-- Contenedor principal del componente -->
  <div id="tabla-personas">
    <!-- Tabla HTML para mostrar la informacion de personas -->
    <div
      v-if="!personas.length"
      class="alert alert-info"
      role="alert"
    >
      No se han agregado personas
    </div>
    <table class="table">
      <!-- Encabezado de la tabla -->
      <thead>
        <!-- nombres de columnas -->
        <tr>
          <th>Nombre</th>
          <th>Apellido</th>
          <th>Email</th>
        </tr>
      </thead>
      <!-- Cuerpo de la tabla con datos dinamicos -->
      <tbody>
        <!-- Iteracion sobre el arreglo de personas utilizando v-for -->
        <tr
          v-for="persona in personas"
          :key="persona.id"
        >
          <td v-if="editando === persona.id">
            <input
              id="persona.nombre"
              v-model="persona.nombre"
              type="text"
              class="form-control"
              data-cy="persona-nombre"
            >
          </td>
          <!-- Celda de datos para el nombre de la persona -->
          <td v-else>
            {{ persona.nombre }}
          </td>

          <td v-if="editando === persona.id">
            <input
              v-model="persona.apellido"
              type="text"
              class="form-control"
            >
          </td>
          <!-- Celda de datos para el apellido de la persona -->
          <td v-else>
            {{ persona.apellido }}
          </td>

          <td v-if="editando === persona.id">
            <input
              v-model="persona.email"
              type="email"
              class="form-control"
            >
          </td>
          <!-- Celda de datos para el correo electronico de la persona -->
          <td v-else>
            {{ persona.email }}
          </td>

          <td v-if="editando === persona.id">
            <button
              class="btn btn-success"
              data-cy="save-button"
              @click="guardarPersona(persona)"
            >
              &#x1F5AB; Guardar
            </button>
            <button
              class="btn btn-secondary ml-2"
              data-cy="cancel-button"
              @click="cancelarEdicion(persona)"
            >
              &#x1F5D9; Cancelar
            </button>
          </td>
          <td v-else>
            <button
              class="btn btn-info"
              data-cy="edit-button"
              @click="editarPersona(persona)"
            >
              &#x1F58A; Editar
            </button>
            <button
              class="btn btn-danger ml-2"
              @click="$emit('delete-persona', persona.id)"
            >
              &#x1F5D1; Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
/* Estilos específicos para este componente */
</style>
