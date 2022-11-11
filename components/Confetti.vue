<template>
  <div ref="confetti-canvas"></div>
</template>

<script>
export default {
  props: {
    confettiCount: {
      default: 2,
      type: Number,
    },
  },
  data() {
    return {
      confettiCanvasObject: null,
    };
  },
  methods: {
    fireConfetti() {
      this.confettiCanvasObject({
        particleCount: 100,
        startVelocity: 30,
        spread: 360,
        origin: {
          x: Math.random(),
          // since they fall down, start a bit higher than random
          y: Math.random() - 0.2,
        },
      });
    },
  },
  created() {
    const confetti = require("canvas-confetti");
    var myConfetti = confetti.create(this.$refs["confetti-canvas"], {
      resize: true,
      useWorker: true,
    });
    this.confettiCanvasObject = myConfetti;
    for (let i = 0; i < this.confettiCount; i++) {
      this.fireConfetti(myConfetti);
    }
  },
};
</script>

<style>
</style>
