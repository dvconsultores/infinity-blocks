<template>
  <v-main id="login">
    <v-window v-model="windowStep" touchless>
      <!-- login window -->
      <v-window-item id="login-window" :value="1">
        <section>
          <img src="~/assets/sources/logos/logo-login.svg" alt="logo" class="mb-16" style="--w: max(150px, 13em)">

          <v-form
            ref="formLogin" class="card divcol"
            style="--bg: var(--primary); --w: 100%; gap: 1em"
            @submit.prevent="login()"
          >
            <div class="divcol" style="gap: .5em">
              <label for="username-login">USUARIO:</label>
              <v-text-field
                id="username-login"
                v-model="formLogin.username"
                :rules="rules.username"
                placeholder="Ingresa tu usuario"
                hide-details solo
                class="username-input"
              ></v-text-field>
            </div>
            
            <div class="divcol" style="gap: .5em">
              <label for="password-login">CONTRASEÑA:</label>
              <v-text-field
                id="password-login"
                v-model="formLogin.password"
                :type="showLogin ? 'text' : 'password'"
                :rules="rules.password"
                placeholder="Ingresa tu contraseña"
                hide-details solo
                hide-spin-buttons
              >
                <template #append>
                  <v-icon size="1.3em" @click="showLogin = !showLogin">
                    mdi-eye{{showLogin ? '' : '-off'}}
                  </v-icon>
                </template>
              </v-text-field>
            </div>
            
            <v-btn
              class="btn align" style="--bg: var(--active)"
              @click="login()">
              Iniciar sesión
            </v-btn>
          </v-form>

          <aside class="divcol center tcenter" style="gap: inherit; --fs: max(15px, 1.25em)">
            <a
              style="--fw: 800"
              @click="
                $store.state.verificationEmail
                ? windowStep = 3
                : $router.push(localePath('/verification-email/:login:recover'))
              ">
              ¿Olvidaste tu contraseña?
            </a>

            <span class="hspan divcol center">
              ¿No tienes cuenta?
              <a style="--fw: 800; --fs: 1em" @click="windowStep = 2">Registrate aquí</a>
            </span>
          </aside>
        </section>
      </v-window-item>


      <!-- register window -->
      <v-window-item id="register-window" :value="2">
        <section>
          <button class="mb-10" @click="$router.go()">
            <img src="~/assets/sources/logos/logo-header.svg" alt="logo" style="--w: max(190px, 17em)">
          </button>

          <v-form ref="formRegister" class="fill_w divcol" style="gap: 1em">
            <div class="divcol" style="gap: .5em">
              <label for="email-register">CORREO <sup>*</sup></label>
              <v-text-field
                id="email-register"
                v-model="formRegister.email"
                solo hide-details
                :rules="rules.email"
              ></v-text-field>
            </div>
            
            <div class="divcol" style="gap: .5em">
              <label class="relative" for="username-register">USUARIO <sup>*</sup>
                <v-tooltip top nudge-top="10px" color="#fff" content-class="custome-tooltip">
                  <template #activator="{ on, attrs }">
                    <img
                      src="~/assets/sources/icons/warning-dark.svg" alt="tip" class="absolute"
                      style="--w: max(16px, .9em); --l: auto; --top: auto; --b: -5px"
                      v-bind="attrs"
                      v-on="on"
                    >
                  </template>
                  
                  <span>
                    Ingresar nombre de usuario
                  </span>
                </v-tooltip>
              </label>
              <v-text-field
                id="username-register"
                v-model="formRegister.username"
                solo hide-details
                :rules="rules.username"
                class="username-input"
              ></v-text-field>
            </div>
            
            <div class="divcol" style="gap: inherit">
              <div class="divcol" style="gap: .5em">
                <label class="relative" for="password-register">CONTRASEÑA <sup>*</sup>
                  <v-tooltip top nudge-top="10px" color="#fff" content-class="custome-tooltip">
                    <template #activator="{ on, attrs }">
                      <img
                        src="~/assets/sources/icons/warning-dark.svg" alt="tip" class="absolute"
                        style="--w: max(16px, .9em); --l: auto; --top: auto; --b: -5px"
                        v-bind="attrs"
                        v-on="on"
                      >
                    </template>
                    
                    <span>
                      Debe incluir mayúsculas, minúsculas, números y mínimo 8 carácteres
                    </span>
                  </v-tooltip>
                </label>
                <v-text-field
                  :id="formRegister.password ? '' : 'password-register'"
                  v-model="formRegister.password"
                  :type="showRegister ? 'text' : 'password'"
                  solo hide-details
                  hide-spin-buttons
                  :rules="rules.password"
                >
                  <template #append>
                    <v-icon size="1.3em" @click="showRegister = !showRegister">
                      mdi-eye{{showRegister ? '' : '-off'}}
                    </v-icon>
                  </template>
                </v-text-field>
              </div>
              
              <v-text-field
                :id="formRegister.password ? 'password-register' : ''"
                v-model="passwordConfirmerRegister"
                :type="showRegister ? 'text' : 'password'"
                solo hide-details
                hide-spin-buttons
                :rules="rules.confirmPasswordRegister"
              ></v-text-field>
            </div>
            
            <div class="divcol" style="gap: .5em">
              <label for="partner-register">PATROCINADOR</label>
              <v-text-field
                id="partner-register"
                v-model="formRegister.partner"
                solo hide-details
              ></v-text-field>
            </div>
            
            <div class="divcol" style="gap: .5em">
              <label for="phone-register">TELÉFONO <sup>*</sup></label>
              <v-text-field
                id="phone-register"
                v-model="formRegister.phone"
                type="number"
                hide-spin-buttons
                solo hide-details
                :rules="rules.phone"
              >
                <template #prepend>
                  <v-select
                    v-model="formRegister.phonePrefix"
                    :items="countryPhoneList"
                    item-text="number"
                    solo hide-details
                    menu-props="auto"
                    append-icon="mdi-chevron-down"
                  >
                    <template #selection="{ item }">
                      <img :src="item.icon" alt="country flag" class="mr-2 aspect" style="--w: 22px; --of: cover; --br: 50%; --b: 1px solid #fff">
                      <span style="--fw: 100">{{item.number}}</span>
                    </template>
                    
                    <template #item="{ item }">
                      <img :src="item.icon" alt="country flag" class="mr-2 aspect" style="--w: 22px; --of: cover; --br: 50%; --b: 1px solid #fff">
                      <span class="not_clr" style="--fw: 100">{{item.number}}</span>
                    </template>
                  </v-select>
                </template>
              </v-text-field>
            </div>
            
            <v-btn
              :disabled="!$refs.formRegister?.validate()"
              class="btn align mt-5" style="--bg: var(--primary); --bg-disabled: var(--primary)"
              @click="register()">
              Registrar
            </v-btn>
          </v-form>
        </section>
      </v-window-item>


      <!-- recover window -->
      <v-window-item id="login-window" :value="3">
        <section>
          <button class="mb-10" @click="$router.go()">
            <img src="~/assets/sources/logos/logo-header.svg" alt="logo" style="--w: max(190px, 17em)">
          </button>

          <v-form
            ref="formRecover" class="card divcol"
            style="--bg: var(--primary); --w: 100%; gap: 1em"
            @submit.prevent="recover()"
          >
            <div class="divcol" style="gap: .5em">
              <label class="relative" for="password-recover">NUEVA CONTRASEÑA:
                <v-tooltip top nudge-top="10px" color="#fff" content-class="custome-tooltip">
                  <template #activator="{ on, attrs }">
                    <img
                      src="~/assets/sources/icons/warning-dark.svg" alt="tip" class="absolute"
                      style="--w: max(16px, .9em); --l: auto; --top: auto; --b: -5px"
                      v-bind="attrs"
                      v-on="on"
                    >
                  </template>
                  
                  <span>
                    Debe incluir mayúsculas, minúsculas, números y mínimo 8 carácteres
                  </span>
                </v-tooltip>
              </label>
              <v-text-field
                id="password-recover"
                v-model="formRecover.password"
                :type="showRecover ? 'text' : 'password'"
                :rules="rules.password"
                placeholder="Ingresa nueva contraseña"
                hide-details solo
                hide-spin-buttons
              >
                <template #append>
                  <v-icon size="1.3em" @click="showRecover = !showRecover">
                    mdi-eye{{showRecover ? '' : '-off'}}
                  </v-icon>
                </template>
              </v-text-field>
            </div>
            
            <div class="divcol" style="gap: .5em">
              <label for="confirm-password-recover">CONFIRMAR CONTRASEÑA:</label>
              <v-text-field
                id="confirm-password-login"
                v-model="passwordConfirmerRecover"
                :type="showRecover ? 'text' : 'password'"
                :rules="rules.confirmPasswordRecover"
                placeholder="Confirma contraseña"
                hide-details solo
                hide-spin-buttons
              ></v-text-field>
            </div>
            
            <v-btn
              class="btn align" style="--bg: var(--active)"
              @click="recover()">
              Recuperar
            </v-btn>
          </v-form>
        </section>
      </v-window-item>
    </v-window>
  </v-main>
</template>

<script>
import computeds from '~/mixins/computeds'

export default {
  name: "LoginPage",
  mixins: [computeds],
  layout: "empty-layout",
  asyncData({ from, store }) {
    let windowStep
    if (from.params.verification?.includes(":recover") && store.state.verificationEmail) windowStep = 3
    else if (from.params.verification?.includes(":register") && store.state.verificationEmail) windowStep = 2

    return {
      windowStep
    }
  },
  data() {
    return {
      windowStep: 1,
      formLogin: {
        username: undefined,
        password: undefined,
      },
      formRegister: {
        email: undefined,
        username: undefined,
        password: undefined,
        partner: undefined,
        phonePrefix: "+593",
        phone: undefined,
      },
      passwordConfirmerRegister: undefined,
      formRecover: {
        password: undefined,
      },
      passwordConfirmerRecover: undefined,
      countryPhoneList: [
        {
          icon: require("~/assets/sources/images/spain-flag.jpg"),
          number: "+593",
        },
        {
          icon: require("~/assets/sources/images/spain-flag.jpg"),
          number: "+58",
        },
        {
          icon: require("~/assets/sources/images/usa-flag.jpg"),
          number: "+2134",
        },
      ],
      showLogin: false,
      showRegister: false,
      showRecover: false,
      rules: {
        confirmPasswordRegister: [
          (v) => !!v || "Field required",
          (v) => /^(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9]).{8,}$/.test(v) || 'Must have uppercase, lowercase, numbers, and a minimum number of 8 characters',
          () => this.formRegister.password === this.passwordConfirmerRegister || 'Password must match',
        ],
        confirmPasswordRecover: [
          (v) => !!v || "Field required",
          () => this.formRecover.password === this.passwordConfirmerRecover || 'Password must match',
        ],
      }
    }
  },
  head() {
    const title = 'Login';
    return {
      title,
    }
  },
  // mounted() {
  //   this.$targetTooltip('.modalSettings img[alt="info"]')
  //   window.addEventListener("resize", () => this.$targetTooltip('.modalSettings img[alt="info"]', 13))
  // },
  methods: {
    login() {
      if (!this.$refs.formLogin.validate()) return this.$alert("cancel", "verifica que los campos sean correctos");
      localStorage.setItem("auth", true)
      this.$router.push(this.localePath('/profile'))
    },
    register() {
      if (!this.$refs.formRegister.validate()) return this.$alert("cancel", "verifica que los campos sean correctos");
      localStorage.setItem("auth", true)
      this.$router.push(this.localePath('/profile'))
    },
    recover() {
      if (!this.$refs.formRecover.validate()) return this.$alert("cancel", "verifica que los campos sean correctos");
      console.log("recovered")
      this.$alert("success", {title: "recovered"})
      this.$router.go()
    },
  }
}
</script>

<style src="~/assets/styles/pages/login.scss" lang="scss" />
