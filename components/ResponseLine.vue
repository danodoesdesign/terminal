<template>
  <div>
    <div
      v-if="type == 'error'"
      id="response-line-error"
      class="inline-block align-text-top"
    >
      {{ terminalTitle }}:&nbsp;<slot />
    </div>
    <div
      v-if="type == 'success'"
      id="response-line-success"
      class="inline-block align-text-top"
    >
      {{ terminalTitle }}:&nbsp;<slot />
    </div>
    <div
      v-if="type == null"
      id="response-line-default"
      class="inline-block align-text-top"
    >
      {{ terminalTitle }}:&nbsp;<slot />
    </div>
    <div
      v-if="type == 'table-line'"
      id="response-line-default"
      class="inline-block align-text-top"
    >
      <slot />
    </div>
    <div
      v-if="type == 'repeated'"
      id="response-line-repeated"
      class="inline-block align-text-top"
    >
      guest@{{ terminalTitle }} %&nbsp;<slot />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    type: String,
  },
  data() {
    return {
      terminalTitle: '',
    }
  },
  methods: {
    setDefaultTerminalTitle() {
      if (this.terminalTitle == '') {
        this.terminalTitle = 'terminal'
      }
    },
  },
  mounted() {
    this.setDefaultTerminalTitle()
  },
}
</script>

<style>
#response-line-error {
  @apply text-red-600;
  outline: none;
  margin-top: -2px;
  &:hover,
  &:focus {
    outline: none;
  }
}
#response-line-success {
  @apply text-green-500;
  outline: none;
  margin-top: -2px;
  &:hover,
  &:focus {
    outline: none;
  }
}
#response-line-default {
  @apply text-white;
  outline: none;
  margin-top: -2px;
  &:hover,
  &:focus {
    outline: none;
  }
}
#response-line-repeated {
  @apply text-gray-600;
  outline: none;
  margin-top: -2px;
  &:hover,
  &:focus {
    outline: none;
  }
}
</style>
