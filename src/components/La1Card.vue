<template>
  <q-card>
    <q-card-section
      :class="`${dense ? 'q-pa-sm' : 'q-pa-lg'} bg-${
        style.headerBgColor
      } text-${style.headerTextColor}`"
    >
      <!--Card Header-->
      <div class="row items-center q-gutter-y-sm">
        <div
        class="text-h6"
          :class="`col-12 ${forceMobile ? '' : 'col-md-5 q-mb-md-none'}`"
          style="display: flex; align-items: center;"
          :style="dense ? 'gap: 1px' : 'gap: 4px'"
        >
          <Icon :Name="Icon" :Html="IconHtml" :Size="dense ? 'xs' : 'md'" :Color="style.headerTextColor"></Icon>
          {{ Title }}
        </div>
        <div
          v-if="`actions` in $slots"
          :class="`col-12 text-right ${forceMobile ? '' : 'col-md-7'}`"
        >
          <slot name="actions"></slot>
        </div>
      </div>
    </q-card-section>

    <!--Main Section-->
    <q-card-section class="card-main-section q-pa-none q-pa-md-md">
      <slot></slot>
    </q-card-section>

    <!--Secondary Sections-->
    <div v-for="(section, name) in $slots" :key="name">
      <q-separator v-if="name != 'actions' && name != 'default'"></q-separator>
      <q-card-section
        class="q-pa-xs q-pa-md-md"
        v-if="name != 'actions' && name != 'default'"
      >
        <slot :name="name"></slot>
      </q-card-section>
    </div>
  </q-card>
</template>

<script>
export default {
  name: "ui-layoutadmin-card",

  props: {
    Title: String,
    Icon: String,
    IconHtml: String,
    HeaderBgColor: String,
    HeaderTextColor: String,
    dense: Boolean,
    forceMobile: Boolean,
  },

  data() {
    return {
      style: {
        headerBgColor: "teal",
        headerTextColor: "white",
      },
    };
  },

  mounted() {
    if (!!this.HeaderBgColor) this.style.headerBgColor = this.HeaderBgColor;
    if (!!this.HeaderTextColor)
      this.style.headerTextColor = this.HeaderTextColor;
  },
};
</script>

<style scoped>
.card-main-section {
  overflow-x: scroll;
}

.text-h6 {
  font-size: 1.1rem;
}
</style>
