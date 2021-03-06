<template>
  <div class="application-wrapper">
    <v-container class="form-wrapper elevation-2">
      <v-form id="app" class="form" ref="loginForm" @submit="onLogin">
        <v-container fluid>
          <v-layout column>
            <v-layout row>
              <v-flex>
                <h3 class="title-login title is-3 has-text-right">
                  {{ $t("CREATE_ACCOUNT.FORM.TITLE") }}
                </h3>
              </v-flex>
            </v-layout>

            <!-- <v-flex>
              <v-text-field
                :label="$t('CREATE_ACCOUNT.LABEL.NAME')"
                v-model="login.name"
                :placeholder="$t('CREATE_ACCOUNT.FORM.NAMEPLACEHOLDER')"
                name="name"
                id="name"
                :rules="[rules.required]"
              ></v-text-field>
            </v-flex> -->

            <v-flex>
              <v-text-field
                :label="$t('CREATE_ACCOUNT.LABEL.USERNAME')"
                v-model="login.username"
                :placeholder="$t('CREATE_ACCOUNT.FORM.USERNAMEPLACEHOLDER')"
                name="username"
                id="username"
                :rules="[rules.required]"
              ></v-text-field>
            </v-flex>

            <v-flex>
              <v-text-field
                :label="$t('CREATE_ACCOUNT.LABEL.EMAIL')"
                v-model="login.email"
                :placeholder="$t('CREATE_ACCOUNT.FORM.EMAILPLACEHOLDER')"
                name="email"
                id="email"
                :rules="[rules.required, rules.email]"
              ></v-text-field>
            </v-flex>

            <v-flex>
              <v-text-field
                :label="$t('CREATE_ACCOUNT.LABEL.PASSWORD')"
                v-model="login.password"
                :placeholder="$t('CREATE_ACCOUNT.FORM.PASSWORDPLACEHOLDER')"
                name="password"
                id="password"
                type="password"
                :rules="[rules.required]"
              ></v-text-field>

              <v-text-field
                :label="$t('CREATE_ACCOUNT.LABEL.PASSWORD_DUPLICATE')"
                v-model="login.password_dupl"
                :placeholder="$t('CREATE_ACCOUNT.FORM.PASSWORDPLACEHOLDER')"
                name="password"
                id="password"
                type="password"
                :rules="[
                  rules.required,
                  val => val === login.password || $t('GLOBAL_ERROR.PASSWORD'),
                ]"
              ></v-text-field>
            </v-flex>

            <v-flex pa-1>
              <p
                style="text-align: justify; background: rgba(100, 100, 100, .2); padding: 10px; text-indent: 15px; font-size: 14px; font-weight: lighter;"
              >
                Ao concluir seu cadastro, você está concordando que seus dados
                sejam utilizados internamente no sistema para facilitar a
                comunicação entre usuários e professores. Seus dados não serão
                compartilhados com nenhuma ferramenta externa.
              </p>
            </v-flex>
          </v-layout>

          <v-layout row wrap justify-end>
            <v-btn dark large color="blue" type="submit" :loading="loading > 0">
              {{ $t("CREATE_ACCOUNT.FORM.SUBMIT") }}
            </v-btn>
          </v-layout>
        </v-container>
      </v-form>
    </v-container>
  </div>
</template>

<script>
import AuthService from "@/services/AuthService";
import ValidationMixin from "@/mixins/ValidationMixin";
import { mapState } from "vuex";

export default {
  name: "Login",
  mixins: [ValidationMixin],
  components: {},
  data() {
    return {
      login: {
        email: "",
        password: "",
        username: "",
        name: "",
      },
      errors: [],
      returnUrl: this.$route.query.returnUrl || "/",
      authService: new AuthService(),
    };
  },
  mounted() {
    this.authService.logout();
  },
  computed: mapState(["loading"]),
  methods: {
    onLogin: function(evt) {
      evt.preventDefault();
      const context = this;

      if (this.$refs.loginForm.validate()) {
        context.authService
          .create(
            context.login.email,
            context.login.password,
            context.login.username
          )
          .then(() => {
            context.authService
              .login(context.login.email, context.login.password)
              .then(() => {
                if (context.returnUrl && context.returnUrl != "/") {
                  window.location = context.returnUrl;
                } else {
                  window.location = "/resume";
                }
              });
          })
          .catch(resp => {
            context.errors = resp.body.errors;
          });
      }
    },
  },
};
</script>

<style scoped>
.application-wrapper {
  width: 100%;
  height: 100%;
  display: flex;

  /* Permalink - use to edit and share this gradient: https://colorzilla.com/gradient-editor/#fafafa+70,7db9e8+80 */
  background: rgb(250, 250, 250); /* Old browsers */
  background: -moz-linear-gradient(
    -45deg,
    rgba(250, 250, 250, 1) 55%,
    rgb(0, 17, 95) 90%
  ); /* FF3.6-15 */
  background: -webkit-linear-gradient(
    -45deg,
    rgba(250, 250, 250, 1) 55%,
    rgba(0, 17, 95, 1) 90%
  ); /* Chrome10-25,Safari5.1-6 */
  background: linear-gradient(
    135deg,
    rgba(250, 250, 250, 1) 55%,
    rgba(0, 17, 95, 1) 90%
  ); /* W3C, IE10+, FF16+, Chrome26+, Opera12+, Safari7+ */
  filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#fafafa', endColorstr='#7db9e8',GradientType=1 ); /* IE6-9 fallback on horizontal gradient */
}

.title-login {
  display: flex;
  align-content: center;
  align-items: center;
  height: 100%;
}
.form-wrapper {
  max-width: 500px;
  /* height: calc(auto + 30px); */
  background: #fafafa;
  justify-content: center;
  align-items: center;
  display: flex;
}

.form-wrapper .form {
  width: 100%;
}

.align-right {
  display: flex;
  justify-content: end;
  align-content: flex-end;
  align-items: flex-end;
}

@media (max-width: 1024px) {
  .form-wrapper {
    max-width: 70%;
  }
}

@media (max-width: 768px) {
  .form-wrapper {
    width: 750%;
    min-width: 350px;
  }
}
</style>
