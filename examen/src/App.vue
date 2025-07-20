<template>
  <div class="container mt-4">
    <ul class="nav nav-tabs mb-4">
      <li class="nav-item">
        <a class="nav-link" :class="{ active: currentTab === 'notas' }" @click="currentTab = 'notas'">Cálculo de calificaciones</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" :class="{ active: currentTab === 'registro' }" @click="currentTab = 'registro'">Formulario de registro</a>
      </li>
    </ul>

    <div v-if="currentTab === 'notas'">
      <h3>Cálculo de calificaciones</h3>
      <form @submit.prevent="calcularPromedio">
        <div v-for="(nota, index) in notas" :key="index" class="mb-3">
          <label :for="'nota' + index" class="form-label">Nota {{ index + 1 }}</label>
          <input
            :id="'nota' + index"
            type="number"
            class="form-control"
            v-model.number="notas[index]"
            min="10"
            max="70"
            required
          />
        </div>
        <div class="mb-3">
          <label for="asistencia" class="form-label">Asistencia %</label>
          <input
            id="asistencia"
            type="number"
            class="form-control"
            v-model.number="asistencia"
            min="0"
            max="100"
            required
          />
        </div>
        <button class="btn btn-primary" type="submit">Calcular</button>
      </form>

      <div v-if="error" class="text-danger mt-3">
        {{ error }}
      </div>
      <div v-if="promedio !== null" class="mt-3">
        <p><strong>El promedio es:</strong> {{ promedio.toFixed(2) }}</p>
        <p><strong>Tu estado es:</strong> {{ estado }}</p>
      </div>
    </div>

    <div v-if="currentTab === 'registro'">
      <h3>Formulario de registro</h3>
      <form @submit.prevent="enviarFormulario">
        <div class="mb-3">
          <label class="form-label">Nombre:</label>
          <input type="text" v-model="form.nombre" class="form-control" />
        </div>
        <div class="mb-3">
          <label class="form-label">Correo:</label>
          <input type="email" v-model="form.correo" class="form-control" />
          <div v-if="errores.correo" class="text-danger">{{ errores.correo }}</div>
        </div>
        <div class="mb-3">
          <label class="form-label">Contraseña:</label>
          <input type="password" v-model="form.contrasena" class="form-control" />
          <div v-if="errores.contrasena" class="text-danger">{{ errores.contrasena }}</div>
        </div>
        <div class="mb-3">
          <label class="form-label">Repetir Contraseña:</label>
          <input type="password" v-model="form.repetirContrasena" class="form-control" />
          <div v-if="errores.repetirContrasena" class="text-danger">{{ errores.repetirContrasena }}</div>
          <div v-if="errores.coincidencia" class="text-danger">{{ errores.coincidencia }}</div>
        </div>
        <button type="submit" class="btn btn-success">Enviar</button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentTab: "notas",
      notas: [null, null, null],
      asistencia: null,
      promedio: null,
      estado: "",
      error: "",
      form: {
        nombre: "",
        correo: "",
        contrasena: "",
        repetirContrasena: "",
      },
      errores: {
        correo: "",
        contrasena: "",
        repetirContrasena: "",
        coincidencia: "",
      },
    };
  },
  methods: {
    calcularPromedio() {
      const notasValidas = this.notas.every(n => n >= 10 && n <= 70);
      const asistenciaValida = this.asistencia >= 0 && this.asistencia <= 100;

      if (!notasValidas || !asistenciaValida) {
        this.promedio = null;
        this.estado = "";
        this.error = "Por favor, ingrese valores válidos para las notas y la asistencia";
        return;
      }

      this.error = "";
      this.promedio =
        this.notas[0] * 0.3 + this.notas[1] * 0.3 + this.notas[2] * 0.4;
      this.estado = this.promedio >= 40 && this.asistencia >= 70 ? "Aprobado" : "Reprobado";
    },
    enviarFormulario() {
      this.errores = {
        correo: "",
        contrasena: "",
        repetirContrasena: "",
        coincidencia: "",
      };

      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      let valido = true;

      if (!emailRegex.test(this.form.correo)) {
        this.errores.correo = "Correo inválido";
        valido = false;
      }
      if (!this.form.contrasena) {
        this.errores.contrasena = "El campo contraseña es requerido";
        valido = false;
      }
      if (!this.form.repetirContrasena) {
        this.errores.repetirContrasena = "El campo repetir contraseña es requerido";
        valido = false;
      }
      if (
        this.form.contrasena &&
        this.form.repetirContrasena &&
        this.form.contrasena !== this.form.repetirContrasena
      ) {
        this.errores.coincidencia = "Las contraseñas no coinciden";
        valido = false;
      }

      if (valido) {
        alert("El registro se ha realizado correctamente");
        this.form = {
          nombre: "",
          correo: "",
          contrasena: "",
          repetirContrasena: "",
        };
      }
    },
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
}
.container {
  max-width: 500px;
}
</style>
