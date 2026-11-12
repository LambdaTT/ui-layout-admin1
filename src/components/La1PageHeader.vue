<template>
  <div class="row">
    <div class="col-12 col-md-6 q-pa-md q-pa-md-none">
      <h4 class="gt-xs q-my-md">{{ pageTitle }}</h4>
      <div class="lt-sm text-h5 q-mt-md">{{ pageTitle }}</div>
    </div>
    <div class="col-12 col-md-6">
      <q-breadcrumbs class="q-mb-md q-pt-lg q-pr-md float-right">
        <q-breadcrumbs-el v-for="(item, index) in breadcrumb" :key="index" :label="item.label" :icon="item.icon"
          :to="item.to" />
      </q-breadcrumbs>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ui-layoutadmin-pageheader',

  props: {
    pageTitle: String,
    breadcrumb: Array
  }
}
</script>

<style lang="scss" scoped>
/* Último item do breadcrumb (página atual) no laranja da marca Cartappio.
   O Quasar envolve cada item num wrapper próprio e marca só o último com a
   classe `q-breadcrumbs--last` — é nele (e no ícone dentro) que aplicamos a cor. */
.q-breadcrumbs :deep(.q-breadcrumbs--last),
.q-breadcrumbs :deep(.q-breadcrumbs--last .q-breadcrumbs__el) {
  color: $brand-orange;
}
</style>
