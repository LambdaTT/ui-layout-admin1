<template>
  <div class="text-grey-9">
    <q-layout v-if="state === 'ready'" view="hHh Lpr lff" container-fluid style="height: 300px"
      class="shadow-2 rounded-borders">
      <HeaderBar @load="load" @loaded="loaded" @toggleDrawer="this.drawerState = !this.drawerState;"
        LogoPath="/resources/img/logo-horizontal.png" :SearchOnHelpFn="searchOnHelp" :LogoutFn="signOut">
      </HeaderBar>
      <Sidebar @load="load" @loaded="loaded" @drawer-hide="drawerState = false" :outerDrawerState="drawerState" />

      <q-page-container>
        <div id="content-wrapper">
          <router-view @load="load" @loaded="loaded" />
        </div>
      </q-page-container>
    </q-layout>
  </div>
</template>

<script>
// Services:
import { auth, permissions } from 'src/modules/lambdatt-ui-iam/services.js'

// Libs:
import { useQuasar } from 'quasar'

export default {
  data() {
    return {
      $q: useQuasar(),
      toLoad: [],
      drawerState: true,
      authTimeOut: null,
      state: 'loading',
    }
  },

  methods: {
    searchOnHelp() {
      var url = "/help/search?terms=" + this.searchTerm;
      window.open(url, '_blank').focus();
    },

    load(evt) {
      console.log('load', evt);
      this.$q.loading.show();
      if (evt && evt != '')
        this.toLoad.push(evt);
    },

    loaded(evt) {
      console.log('loaded', evt);
      var index = this.toLoad.indexOf(evt);

      if (index != -1) this.toLoad.splice(index, 1);

      if (this.toLoad.length == 0)
        this.$q.loading.hide();

    },

    signOut() {
      return auth.logout(this)
        .then(() => location.href = '/login');
    },

    inactivityHandler() {
      var debounceTimeout = null;
      var goToRoute = null;

      this.$eventbroadcaster.$on('http-request-sent', (reqPromise) => {
        reqPromise.catch((err) => {
          goToRoute = goToRoute ?? this.$route.path;
          if (!!debounceTimeout) {
            clearTimeout(debounceTimeout);
            debounceTimeout = null;
          }
          debounceTimeout = setTimeout(() => {
            if (err.response?.status == 401 && !(err.config?.url.includes('/iam/auth/v1/log'))) {
              this.$router.push(`/login?goTo=${goToRoute}`);

              this.$utils.notify({
                message: 'Sua sessão expirou. Por favor, entre novamente.',
                type: 'warning',
                position: 'top-right',
              });
            }
          }, 200);
        });
      });
    },

    loadHandler() {
      this.$eventbroadcaster.$on('load', this.load);
      this.$eventbroadcaster.$on('loaded', this.loaded);
    }
  },

  async mounted() {
    this.$q.loading.show();

    try {
      await auth.authenticate(this);
      await permissions.getUserPermissions();
    } catch (error) {
      console.error(error);
    }

    setTimeout(() => {
      this.loaded('init');
    }, 300);

    // Handle Inactivity
    this.inactivityHandler();
    this.loadHandler();

    this.state = 'ready';
  },

}
</script>
<style scoped>
#content-wrapper {
  background-color: #e9ecef;
}
</style>
