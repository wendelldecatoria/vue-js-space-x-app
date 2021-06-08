<template>
  <div class="lds-ring">
    <div :style="style" />
    <div :style="style" />
    <div :style="style" />
    <div :style="style" />
  </div>
</template>

<script>
export default {
  name: 'Spinner',
  props: {
    theme: {
      type: String,
      default: '#1976d2'
    }
  },
  computed: {
    style() {
      return {
        border: `0.1875rem solid ${this.theme}`,
        borderColor: `${this.theme} transparent transparent transparent`
      };
    }
  }
};
</script>

<style lang="scss">
.lds-ring {
  display: block;
  height: 1.5em;
  margin: auto;
  position: relative;
  width: 1.5em;

  & > div {
    box-sizing: border-box;
    display: block;
    position: absolute;
    width: 1.5em;
    height: 1.5em;
    /* margin: 3px; */
    border-radius: 50%;
    animation: lds-ring 1.2s cubic-bezier(0.5, 0, 0.5, 1) infinite;

    &:first-child {
      animation-delay: -0.45s;
    }

    &:nth-child(2) {
      animation-delay: -0.3s;
    }

    &:last-child {
      animation-delay: -0.15s;
    }
  }
}

@keyframes lds-ring {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
