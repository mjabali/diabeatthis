<template>
  <span v-if="valueOnly">{{ average.toFixed() }}</span>
  <div v-else class="text-center">
    <p class="muted vertical float-left mt-3">Average</p>
    <p class="large">{{ average.toFixed() }}</p>
    <p class="muted">mg/dL</p>
  </div>
</template>

<script>
export default {
  name: "InRangeDay",
  props: {
    history: {
      type: Array,
      required: true,
      default: () => [
        {
          trend: { name: "Flat", desc: "steady", arrow: "→" },
          mgdl: 0,
          mmol: 0,
          time: "2000-01-01T00:00:01.000Z",
        },
        {
          trend: { name: "Flat", desc: "steady", arrow: "→" },
          mgdl: 0,
          mmol: 0,
          time: "2000-01-01T00:00:01.000Z",
        },
      ],
    },
    valueOnly: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      average: 0,
    };
  },
  watch: {
    // Watch for changes in the readings prop and update chartData accordingly
    history(newHistory) {
      this.updateAverage(newHistory);
    },
  },
  methods: {
    updateAverage(history) {
      if (!history || history.length === 0) {
        this.average = 0;
      }

      const sum = history.reduce((acc, reading) => acc + reading.mmol, 0);
      this.average = Math.round((sum / history.length) * 10) / 10;
    },
  },
  mounted() {
    this.updateAverage(this.history);
  },
};
</script>

<style scoped>
.muted {
  opacity: 0.5;
  font-size: 0.9em;
}

.vertical {
  font-size: 0.9em;
  writing-mode: vertical-rl;
  text-orientation: mixed;
  transform: rotate(180deg);
}

.large {
  font-size: 2.5em;
}
</style>
