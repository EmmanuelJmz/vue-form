<script setup>
import HelloWorld from "./components/HelloWorld.vue";
import TheWelcome from "./components/TheWelcome.vue";
</script>

<template>
  <b-container>
    <div>
      <b-form
        @submit.prevent="verificarFormulario"
        action="https://vuejs.org/"
        method="post"
        novalidate
      >
        <b-alert variant="danger" dismissible :show="errors.length > 0">
          <b>{{
            errors.length > 1
              ? "Por favor corrige los siguientes errores:"
              : "Por favor corrige el siguiente error:"
          }}</b>
          <ul>
            <li v-for="error in errors" :key="error">{{ error }}</li>
          </ul>
        </b-alert>

        <b-row>
          <b-col md="6">
            <b-form-group
              id="name-group"
              label="Nombre"
              label-for="name"
              :state="name ? null : false"
            >
              <b-form-input
                id="name"
                v-model="name"
                type="text"
                name="name"
                required
              ></b-form-input>
            </b-form-group>
          </b-col>
          <b-col md="6">
            <b-form-group
              id="lastName-group"
              label="Apellido"
              label-for="lastName"
              :state="lastname ? null : false"
            >
              <b-form-input
                id="lastName"
                v-model="lastname"
                type="text"
                name="lastName"
              ></b-form-input>
            </b-form-group>
          </b-col>
        </b-row>

        <b-form-group id="address-group" label="Dirección" label-for="address">
        </b-form-group>

        <b-form-group
          id="birthdate-group"
          label="Fecha de Nacimiento"
          label-for="birthdate"
          :state="validAge ? null : false"
        >
          <b-form-input
            id="birthdate"
            v-model="birthdate"
            type="date"
            name="birthdate"
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group
          id="email-group"
          label="Correo Electrónico"
          label-for="email"
          :state="validEmail(email) ? null : false"
        >
          <b-form-input
            id="email"
            v-model="email"
            type="email"
            name="email"
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group
          id="phone-group"
          label="Número de Teléfono"
          label-for="phone"
          :state="validPhone(phone) ? null : false"
        >
          <b-form-input
            id="phone"
            v-model="phone"
            type="tel"
            name="phone"
            required
          ></b-form-input>
        </b-form-group>

        <b-form-group
          id="photo-group"
          label="Foto (PNG)"
          label-for="photo"
          :state="validPhoto ? null : false"
        >
          <b-form-file
            id="photo"
            v-model="photo"
            type="file"
            accept=".png"
            @change="handleFileChange"
            required
          ></b-form-file>
        </b-form-group>

        <b-button type="submit" variant="primary">Enviar</b-button>
      </b-form>
    </div>
  </b-container>
</template>

<script>
import Vue from "vue";

export default Vue.extend({
  data() {
    return {
      errors: [],
      name: null,
      lastname: null,
      birthdate: null,
      email: null,
      phone: null,
      photo: null,
      validAge: false,
      validPhoto: false,
    };
  },
  methods: {
    verificarFormulario: function (e) {
      this.errors = [];

      if (!this.name) {
        this.errors.push("Nombre es obligatorio.");
      }

      if (!this.lastname) {
        this.errors.push("Apellido es obligatorio.");
      }

      if (!this.birthdate) {
        this.errors.push("Fecha de Nacimiento es obligatoria.");
      } else {
        const hoy = new Date();
        const fechaNacimiento = new Date(this.birthdate);
        const edad = hoy.getFullYear() - fechaNacimiento.getFullYear();
        const diferenciaMes = hoy.getMonth() - fechaNacimiento.getMonth();
        if (
          diferenciaMes < 0 ||
          (diferenciaMes === 0 && hoy.getDate() < fechaNacimiento.getDate())
        ) {
          this.validAge = edad - 1;
        } else {
          this.validAge = edad;
        }
        if (this.validAge < 18) {
          this.errors.push("La edad debe ser 18 o más.");
        }
      }

      if (!this.email) {
        this.errors.push("Correo Electrónico es obligatorio.");
      } else if (!this.validEmail(this.email)) {
        this.errors.push("Por favor ingresa una dirección de correo válida.");
      }

      if (!this.phone || this.phone.length < 10) {
        this.errors.push(
          "El número de teléfono debe tener al menos 10 caracteres."
        );
      }

      if (!this.photo || !this.validPhoto) {
        this.errors.push("La foto es obligatoria y debe tener menos de 3 MB.");
      }

      if (!this.errors.length) {
        return true;
      }

      e.preventDefault();
    },

    validEmail: function (email) {
      const re =
        /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(String(email).toLowerCase());
    },

    validPhone: function (phone) {
      return true;
    },

    handleFileChange(event) {
      const fileInput = event.target;
      if (fileInput.files.length > 0) {
        const file = fileInput.files[0];
        const tamañoMaximoMB = 3;

        if (file.size > tamañoMaximoMB * 1024 * 1024) {
          this.validPhoto = false;
          this.errors.push(
            `El tamaño de la foto debe ser menos de ${tamañoMaximoMB} MB.`
          );
        } else {
          this.validPhoto = true;
        }
      }
    },
  },
});
</script>

<style></style>
