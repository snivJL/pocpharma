<template>
  <button
    :disabled="disabled"
    :class="disabled ? 'btn btn-disabled' : 'btn'"
    @click="onClick"
  >
    {{ text }}
  </button>
</template>

<script>
export default {
  props: { text: String, disabled: Boolean },
  methods: {
    onClick() {
      this.$emit("check-fengshui");
    },
  },
};
</script>
