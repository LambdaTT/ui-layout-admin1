<template>
  <q-header elevated class="bg-white">
    <q-toolbar class="q-pa-sm text-grey-8">
      <!-- Btn: toggle menu -->
      <q-btn flat @click="$emit('toggle-drawer')" round dense icon="menu" />

      <!-- Main logo -->
      <q-toolbar-title>
        <span class="cursor-pointer" v-on:click="navToHome()">
          <q-img
            class="main-logo vertical-middle"
            alt="Logotipo"
            :src="LogoPath"
          />
          <q-tooltip>Ir para a Home</q-tooltip>
        </span>
      </q-toolbar-title>

      <!-- Input: Search on help (Desktop Only) -->
      <q-toolbar-title class="gt-sm" v-if="!!SearchOnHelpFn">
        <q-input
          disable
          label-color="grey-8"
          outlined
          v-model="searchTerm"
          label="Pesquisar na ajuda"
        >
          <template v-slot:append>
            <q-btn flat size="sm">
              <q-icon
                @click="SearchOnHelpFn()"
                name="fas fa-search"
                color="grey-8"
              />
            </q-btn>
          </template>
        </q-input>
      </q-toolbar-title>

      <!-- Btn: Search on help (For Mobile) -->
      <q-btn class="lt-md q-pa-md" flat round size="sm" v-if="!!SearchOnHelpFn">
        <q-tooltip>Pesquisar na ajuda</q-tooltip>
        <q-icon name="fas fa-search" color="grey-8" />
        <q-menu>
          <q-input
            color="grey-3"
            label-color="grey-8"
            outlined
            v-model="searchTerm"
            label="Pesquisar na ajuda"
          >
            <template v-slot:append>
              <q-btn v-on:click="SearchOnHelpFn()" flat size="sm">
                <q-icon name="fas fa-search" color="grey-8" />
              </q-btn>
            </template>
          </q-input>
        </q-menu>
      </q-btn>

      <!-- Actions Slot -->
      <slot name="actions"></slot>

      <!-- Btn: Full screen -->
      <q-btn
        class="q-pa-md"
        flat
        round
        icon="fas fa-expand-arrows-alt"
        size="sm"
        @click="fullScreen()"
      >
        <q-tooltip>Tela cheia</q-tooltip>
      </q-btn>

      <!-- Btn: Sign out -->
      <q-btn
        class="q-pa-md"
        flat
        round
        icon="fas fa-sign-out-alt"
        size="sm"
        @click="logout()"
      >
        <q-tooltip>Sair</q-tooltip>
      </q-btn>
    </q-toolbar>
  </q-header>
</template>

<script>
export default {
  name: "ui-layoutadmin-headerbar",

  props: {
    LogoPath: String,
    SearchOnHelpFn: Function,
  },

  data() {
    return {
      searchTerm: null,
    };
  },

  methods: {
    navToHome() {
      location.href = "/";
    },

    fullScreen() {
      // if already full screen; exit
      // else go fullscreen
      if (
        document.fullscreenElement ||
        document.webkitFullscreenElement ||
        document.mozFullScreenElement ||
        document.msFullscreenElement
      ) {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        } else if (document.mozCancelFullScreen) {
          document.mozCancelFullScreen();
        } else if (document.webkitExitFullscreen) {
          document.webkitExitFullscreen();
        } else if (document.msExitFullscreen) {
          document.msExitFullscreen();
        }
        element.style.overflowY = "hidden";
      } else {
        var element = document.getElementById("content-wrapper");
        if (element.requestFullscreen) {
          element.requestFullscreen();
        } else if (element.mozRequestFullScreen) {
          element.mozRequestFullScreen();
        } else if (element.webkitRequestFullscreen) {
          element.webkitRequestFullscreen(Element.ALLOW_KEYBOARD_INPUT);
        } else if (element.msRequestFullscreen) {
          element.msRequestFullscreen();
        }
        element.style.overflowY = "scroll";
      }
    },

    async logout() {
      if (!confirm("Deseja encerrar seu acesso?")) return false;

      this.$getService("toolcase/loader").load("logout");

      var url = this.$getModule("iam").endpoints().AUTH.LOGOUT;

      if (localStorage.getItem("authtoken"))
        url += "?token=" + localStorage.getItem("authtoken");

      try {
        await this.$getService("toolcase/http").delete(url);
      } catch (err) {
        localStorage.removeItem("authtoken");
        localStorage.removeItem("xsrf_token");
        localStorage.removeItem("iam_session_key");
        localStorage.removeItem("regularPermissions");
        localStorage.removeItem("customPermissions");
        this.$getService("toolcase/loader").loaded("logout");
        location.href = "/login";
      }
    },
  },
};
</script>

<style scoped>
.main-logo {
  max-width: 155px;
}

.img-size-avatar {
  margin-top: -20px;
  width: 50px;
  height: 50px;
}

.callout-info {
  border-left: 5px solid #117a8b;
}

.callout-warning {
  border-left: 5px solid #d39e00;
}

.media {
  display: -ms-flexbox;
  display: flex;
  -ms-flex-align: start;
  align-items: flex-start;
}

.media-body {
  -ms-flex: 1;
  flex: 1;
}

.img-circle {
  border-radius: 50%;
}

.mr-3 {
  margin-right: 1rem;
}
</style>
