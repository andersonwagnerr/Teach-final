<template>
  <!--Header 
  <div>
     
    <div class="header bg-purple py-7 py-lg-8 pt-lg-9">
      <div class="container">
        <div class="header-body text-center mb-5">
          <div class="row justify-content-center">
            <div class="col-xl-5 col-lg-6 col-md-8 px-5">
              <h1 class="text-white">
                Bem-vindo !
              </h1>
              <p class="text-lead text-white">
                Faça login para acessar o painel de administração.
              </p>
            </div>
          </div>
        </div>
      </div>
      <div class="separator separator-bottom separator-skew zindex-100">
        <svg
          x="0"
          y="0"
          viewBox="0 0 2560 100"
          preserveAspectRatio="none"
          version="1.1"
          xmlns="http://www.w3.org/2000/svg">
          <polygon
            class="fill-default"
            points="2560 0 2560 100 0 100"/>
        </svg>
      </div>
  </div>-->
  <!-- Page content -->
  <div class="container mt--8 pb-5">
    <div class="row justify-content-center">
      <div class="col-lg-5 col-md-7">
        <div class="card bg-secondary border-0 mb-0">
          <div class="card-header bg-transparent">
            <div class="text-center mt-2 mb-2">Entre com sua conta</div>
          </div>
          <div class="card-body px-lg-5 py-lg-5">
            <form role="form" @submit.prevent="handleSubmit">
              <base-input
                v-validate="'required|email|min:12|max:64'"
                v-model="loginForm.email"
                :error="getError('e-mail')"
                :valid="isValid('e-mail')"
                name="e-mail"
                class="mb-3"
                prepend-icon="ni ni-email-83"
                placeholder="E-mail"
                addon-left-icon="ni ni-email-83"
              />

              <base-input
                v-validate="'required|min:6|max:64'"
                v-model="loginForm.password"
                :error="getError('senha')"
                :valid="isValid('senha')"
                name="senha"
                class="mb-3"
                prepend-icon="ni ni-lock-circle-open"
                type="password"
                placeholder="Senha"
                addon-left-icon="ni ni-lock-circle-open"
              />

              <base-checkbox v-model="loginForm.rememberMe">Lembrar-me</base-checkbox>

              <api-errors :single-error="loginError" :alert-classes="'mt-4 py-3 mb-1'" />

              <div v-if="userId" class="mt-3 text-center">
                <p class="m-0 nav-link font-weight-light" @click="resendEmail()">
                  Você não recebeu o email?
                  <br />Clique aqui para reenviá-lo.
                </p>
              </div>

              <div class="text-center">
                <base-button
                  :disabled="loggingIn"
                  type="primary"
                  native-type="submit"
                  size="lg"
                  class="my-4 btn-block"
                >Entrar</base-button>
              </div>
            </form>
          </div>
        </div>
        <div class="row mt-3">
          <div class="col-6">
            <router-link to="/password-reset" class="text-light">
              <small>Esqueceu a senha ?</small>
            </router-link>
          </div>
          <div class="col-6 text-right">
            <router-link to="/register" class="text-light">
              <small>Crie uma conta</small>
            </router-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from "vuex";
import ApiService from "../../../services/api.service";

export default {
  data() {
    return {
      loginForm: {
        email: "",
        password: ""
      }
    };
  },
  computed: {
    ...mapState({
      loginError: state => state.account.status.loginError,
      loggingIn: state => state.account.status.isLoggingIn,
      userId: state => state.account.status.userId
    })
  },
  methods: {
    handleSubmit(e) {
      this.$validator.validate().then(valid => {
        if (valid) this.$store.dispatch("account/login", this.loginForm);
      });
    },
    resendEmail() {
      if (!this.userId) return;
      ApiService.post(`/auth/verify/email/resend/${this.userId}`)
        .then(res => {
          if (res.status === 200) {
            this.$notify({
              type: "success",
              message: `Um email de confirmação do registro foi reenviado!`
            });
          }
        })
        .catch(err => {
          this.$notify({
            type: "danger",
            message: `<b>Erro !</b> ${err.data.message || err} !`
          });
        });
    },
    getError(name) {
      return this.errors.first(name);
    },
    isValid(name) {
      return this.validated && !this.errors.has(name);
    }
  }
};
</script>

<style scoped>
</style>
