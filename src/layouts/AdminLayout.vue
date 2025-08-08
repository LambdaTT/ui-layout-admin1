<template>
  <div class="text-grey-9">
    <q-layout v-if="state === 'ready'" view="hHh Lpr lff" container-fluid style="height: 300px"
      class="shadow-2 rounded-borders">
      <La1HeaderBar @load="load" @loaded="loaded" @toggleDrawer="this.drawerState = !this.drawerState;"
        LogoPath="/resources/img/main-logo.png" :SearchOnHelpFn="searchOnHelp" :LogoutFn="signOut">
      </La1HeaderBar>
      <La1Sidebar @load="load" @loaded="loaded" @drawer-hide="drawerState = false" :outerDrawerState="drawerState" />

      <q-page-container>
        <div id="content-wrapper">
          <router-view @load="load" @loaded="loaded" />
        </div>
      </q-page-container>
    </q-layout>
  </div>
</template>

<script>
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
      return this.$getService('iam/auth').logout(this)
        .then(() => location.href = '/login');
    },

    inactivityHandler() {
      var debounceTimeout = null;
      var goToRoute = null;

      this.$getService('toolcase/eventbroadcaster').$on('http-request-sent', (reqPromise) => {
        reqPromise.catch((err) => {
          goToRoute = goToRoute ?? this.$route.path;
          if (!!debounceTimeout) {
            clearTimeout(debounceTimeout);
            debounceTimeout = null;
          }
          debounceTimeout = setTimeout(() => {
            if (err.response?.status == 401 && !(err.config?.url.includes('/iam/auth/v1/log'))) {
              this.$router.push(`/login?goTo=${goToRoute}`);

              this.$getService('toolcase/utils').notify({
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
      this.$getService('toolcase/eventbroadcaster').$on('load', this.load);
      this.$getService('toolcase/eventbroadcaster').$on('loaded', this.loaded);
    }
  },

  async mounted() {
    this.$q.loading.show();

    try {
      await this.$getService('iam/auth').authenticate(this);
      await this.$getService('iam/permissions').getUserPermissions();
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
