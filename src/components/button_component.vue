<template>
  <button v-on:click="onLick(functionData)">{{ functionData }}</button>
</template>

<script>
export default {
  name: 'ButtonComponent',
  props: {
    functionData: {type: String || Number},
  },
  methods: {
    onLick: function (variable) {
      this.$emit('send-message', variable);
    }
  },
}
</script>

