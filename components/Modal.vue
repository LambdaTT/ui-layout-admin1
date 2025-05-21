<template>
  <q-dialog backdrop-filter="blur(4px) contrast(40%)" :full-width="FullWidth" @hide="hideFn" @show="showFn"
    :persistent="Persistent" v-model="show">
    <q-card style="min-width: 360px">
      <q-toolbar class="full-width bg-teal text-white">
        <q-avatar v-if="!!Icon">
          <q-icon :name="Icon"></q-icon>
        </q-avatar>

        <q-toolbar-title v-if="!!Title">{{ Title }}</q-toolbar-title>

        <q-btn flat round dense icon="close" v-close-popup />
      </q-toolbar>

      <q-card-section class="q-pa-none q-pa-md-md">
        <slot></slot>
      </q-card-section>

      <q-card-section class="q-pt-none">
        <div class="row justify-end">
          <div class="col-12 col-md q-py-xs-xs q-px-md-xs">
            <q-btn class="full-width" dense label="Fechar" color="grey-8" icon="close" v-close-popup>
            </q-btn>
          </div>
          <div v-show="!HideActions" v-for="action in visibleActions" :key="action.label" class="col-12 q-py-xs-xs q-px-md-xs col-md">
            <q-btn class="full-width" dense :label="action.label" :color="action.color" :icon="action.icon"
              @click="action.fn">
            </q-btn>
          </div>
        </div>
      </q-card-section>
    </q-card>
  </q-dialog>
</template>
<script>
export default {
  name: 'ui-layoutadmin-modal',

  props: {
    Title: String,
    Icon: String,
    Persistent: Boolean,
    Actions: Array,
    Data: Object,
    HideActions: Boolean,
    modelValue: Boolean,
    FullWidth: Boolean
  },

  data() {
    return {
      show: false
    }
  },

  watch: {
    show(val) {
      this.$emit('update:model-value', val);
    },

    modelValue(val) {
      this.show = val;
    }
  },

  computed: {
    visibleActions() {
      if (!this.Actions) return [];
      return this.Actions.filter(obj => !obj.hide);
    },
  },

  methods: {
    hideFn() {
      setTimeout(() => this.$emit('hide'), 100);
    },

    showFn() {
      setTimeout(() => this.$emit('show'), 100);
    }
  },
}
</script>