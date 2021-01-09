<template>
  <div class="gradient cursor-pointer" ref="party" @click="sendExitEmit">
    <div
      id="dance"
      class="text-6xl flex flex-col items-center justify-center font-black min-h-screen"
    >
      <p class="text-center">STOP DANCING</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      audio: new Audio('tst.mp3'),
    }
  },
  mounted() {
    this.audio.play()
    this.$refs.party.focus()
  },
  destroyed() {
    this.audio.pause()
  },
  methods: {
    sendExitEmit() {
      this.$emit('exit')
      console.log('exit emit sent')
    },
  },
}
</script>

<style scoped>
#dance {
  font-family: Arial, Helvetica, sans-serif;
  @apply text-white;
}

.gradient {
  height: 100%;
  width: 100%;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  position: absolute;
  background: linear-gradient(
    124deg,
    #ff2400,
    #e81d1d,
    #e8b71d,
    #e3e81d,
    #1de840,
    #1ddde8,
    #2b1de8,
    #dd00f3,
    #dd00f3
  );
  background-size: 1800% 1800%;

  -webkit-animation: rainbow 6s ease infinite;
  -z-animation: rainbow 6s ease infinite;
  -o-animation: rainbow 16s ease infinite;
  animation: rainbow 6s ease infinite;
}

@-webkit-keyframes rainbow {
  0% {
    background-position: 0% 82%;
  }
  50% {
    background-position: 100% 19%;
  }
  100% {
    background-position: 0% 82%;
  }
}
@-moz-keyframes rainbow {
  0% {
    background-position: 0% 82%;
  }
  50% {
    background-position: 100% 19%;
  }
  100% {
    background-position: 0% 82%;
  }
}
@-o-keyframes rainbow {
  0% {
    background-position: 0% 82%;
  }
  50% {
    background-position: 100% 19%;
  }
  100% {
    background-position: 0% 82%;
  }
}
@keyframes rainbow {
  0% {
    background-position: 0% 82%;
  }
  50% {
    background-position: 100% 19%;
  }
  100% {
    background-position: 0% 82%;
  }
}
</style>
