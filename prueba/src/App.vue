<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row align="center" justify="center">
          <v-col cols="6">
            <v-alert
              type="error"
              v-text="error"
              v-if="error"
            ></v-alert>
            <v-form @submit.prevent="sendData" v-model="valid" lazy-validation>
              <h3>Formulario de contacto</h3>
              <v-divider></v-divider>
              <v-text-field
                v-for="(field, i) in fields"
                :key="i"
                :label="field.label"
                :rules="field.rules"
                v-model="aboutMsg[field.prop]"
              ></v-text-field>

              <v-btn
                type="submit"
                :disabled="!valid || loading"
                :loading="loading"
              >Enviar</v-btn>
            </v-form>
          </v-col>
          <v-col cols="6">
            <h3>Mensajes enviados</h3>
            <v-divider></v-divider>
            <v-data-table
              :headers="headers"
              :loading="loading"
              :items="aboutMessages"
            ></v-data-table>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    error: '',
    aboutMsg: {
      name: '',
      phone: '',
      email: '',
      subject: '',
      msg: '',
    },
    aboutMessages: [],
    headers: [
      { text: 'Nombre completo', value: 'name', },
      { text: 'Correo electrónico', value: 'email', },
      { text: 'Teléfono', value: 'phone', },
      { text: 'Asunto', value: 'subject', },
      { text: 'Mensaje', value: 'msg', },
    ],
    valid: false,
    loading: false,
    fields: [
      {
        label: 'Nombre completo',
        prop: 'name',
        rules: [ v => !!v || 'El campo es requerido', ],
      }, {
        label: 'Correo electrónico',
        prop: 'email',
        rules: [ v => !!v || 'El campo es requerido', ],
      }, {
        label: 'Teléfono',
        prop: 'phone',
        rules: [ v => !!v || 'El campo es requerido', ],
      }, {
        label: 'Asunto',
        prop: 'subject',
        rules: [ v => !!v || 'El campo es requerido', ],
      }, {
        label: 'Mensaje',
        prop: 'msg',
        rules: [ v => !!v || 'El campo es requerido', ],
      }
    ],
  }),
  methods: {
    async sendData() {
      this.error = '';
      this.loading = true;
      const data = new FormData();
      data.append('name', this.aboutMsg.name);
      data.append('email', this.aboutMsg.email);
      data.append('phone', this.aboutMsg.phone);
      data.append('subject', this.aboutMsg.subject);
      data.append('msg', this.aboutMsg.msg);

      try {
        const res = await fetch('//devlobo.000webhostapp.com/post.php', {
          method: 'POST',
          body: data,
        });
        const result = await res.json();
        if (result.ok) {
          this.fetchData();
        } else {
          this.error = result.msg;
        }
      } catch (e) {
        this.error = 'Hubo un error';
      } finally {
        this.loading = false;
      }
    },
    async fetchData() {
      this.error = '';
      this.loading = true;
      try {
        const r = await fetch('//devlobo.000webhostapp.com/get.php');
        this.aboutMessages = await r.json();
      } catch (e) {
        this.error = 'Hubo un error';
      } finally {
        this.loading = false;
      }
    },
  },
  created() {
    this.fetchData();
  },
}
</script>