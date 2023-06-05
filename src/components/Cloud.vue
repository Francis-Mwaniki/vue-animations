<template>
  <div>
    <div id="speed">
      <div class="fill" :style="{ height: fillHeight }"></div>
    </div>
    <div id="clouds">
      <div class="cloud" v-for="cloud in clouds" :key="cloud.id" :style="cloud.style"></div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cloudCount: 100,
      players: [],
      playbackRate: undefined,
      maxPlaybackRate: 10,
      clouds: []
    };
  },
  computed: {
    fillHeight() {
      return ((this.playbackRate || 0) / this.maxPlaybackRate) * 100 + "%";
    },
  },
  mounted() {
    for (let i = 0; i < this.cloudCount; ++i) {
      const cloud = this.createCloud();
      this.clouds.push(cloud);
      const player = this.animateCloud(cloud);
      this.players.push(player);
    }
    this.setRate(1);
    document.body.addEventListener("wheel", this.handleWheel);
  },
  methods: {
    createCloud() {
      const cloud = {
        id: Math.random(),
        style: {
          fontSize: Math.floor(Math.random() * 12) + 4 + "px",
          transform: `scale(${1.0 - Math.random() / 2})`,
          top: `${Math.random() * 100}%`,
          zIndex: Math.floor(Math.random() * 1000),
        },
      };
      return cloud;
    },
    animateCloud(cloud) {
      const scale = 1.0 + Math.random() / 5;
      const player = cloud.animate(
        [
          { transform: "translate(-20vw) scale(1)" },
          { transform: `translate(50vw) scale(${scale})` },
          { transform: "translate(120vw) scale(1)" },
        ],
        {
          duration: Math.random() * 5000 + 40000,
          easing: "cubic-bezier(0.42, 0, 0.58, 1)",
          iterations: Infinity,
          delay: Math.random() * 50000 - 50000,
        }
      );
      return player;
    },
    setRate(rate) {
      if (rate < 0) {
        rate = 0;
      } else if (rate > +this.maxPlaybackRate) {
        rate = +this.maxPlaybackRate;
      }
      this.playbackRate = rate;

      this.players.forEach((player) => {
        player.playbackRate = this.playbackRate;
        if (this.playbackRate === 0) {
          player.pause();
        } else {
          player.play();
        }
      });
    },
    handleWheel(ev) {
      ev.preventDefault();

      const amount = ev.deltaY + ev.deltaX;
      const delta = amount / 1000;

      this.setRate((this.playbackRate || 0) + delta);
    },
  },
};
</script>

<style>
body {
  margin: 0;
  background: linear-gradient(#3b0097, #e03a85);
  overflow: hidden;
  min-height: 100vh;
  cursor: pointer;
}

#speed {
  position: fixed;
  top: 50%;
  transform: translateY(-50%);
  height: 400px;
  max-height: 50vh;
  left: -12px;
  background: rgba(255, 255, 255, 0.5);
  border-radius: 4px;
  border: 4px solid transparent;
  width: 24px;
  z-index: 20000;
  box-shadow: 0 0 4px rgba(0, 0, 0, 0.5);
}

#speed .fill {
  background: #ccc;
  border-radius: 2px;
}

.holder {
  position: absolute;
}

.cloud {
  color: white;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: currentColor;
  transform: translate(-50%, -50%);
  box-shadow: 0 0 24px black;
}

.cloud::before,
.cloud::after {
  content: "";
  position: absolute;
  width: 100%;
  top: 15%;
  height: 80%;
  background: currentColor;
  border-radius: 50%;
}

.cloud::before {
  left: -1em;
}

.cloud::after {
  right: -1em;
}
</style>
