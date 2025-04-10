<template>
  <v-card>
    <v-card-text>
      <div class="float-right text-end class-muted">
        <p>{{ now }}</p>
        <p v-if="latestEntry && secondLatestEntry">
          &Delta;: {{ delta }} mg/dL
        </p>
      </div>
      <p :class="{ 'large-text': true, stale: isReadingStale }">
        {{ latestEntry.mmol.toFixed() }}
        <span v-html="adjustArrows(latestEntry.trend.arrow)"></span>
      </p>
    </v-card-text>
  </v-card>
</template>

<script>
import moment from "moment";

export default {
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
  },
  data() {
    return {
      now: "&nbsp;",
    };
  },
  methods: {
    updateNow() {
      this.now = moment(this.history[this.history.length - 1].time).fromNow();
    },
    adjustArrows(arrow) {
      if (arrow === "↑") return "&uarr;";
      if (arrow === "↓") return "&darr;";
      if (arrow === "↗") return "&#8599;";
      if (arrow === "↘") return "&#8600;";
      if (arrow === "→") return "&rarr;";
      if (arrow === "⮅") return "&#8648;";
      if (arrow === "⮇") return "&#8650;";
      return "?";
    },
  },
  computed: {
    latestEntry() {
      return this.history[this.history.length - 1] || null;
    },
    secondLatestEntry() {
      return this.history[this.history.length - 2] || null;
    },
    delta() {
      if (!this.latestEntry || !this.secondLatestEntry) return null;
      const diff = this.latestEntry.mmol - this.secondLatestEntry.mmol;
      return diff > 0 ? `+${diff.toFixed()}` : diff.toFixed();
    },
    isReadingStale() {
      if (!this.latestEntry) return false;
      const fifteenMinutesAgo = moment().subtract(15, "minute");
      return moment(this.latestEntry.time).isBefore(fifteenMinutesAgo);
    },
  },
  watch: {
    history: {
      immediate: true,
      handler() {
        this.updateNow();
      },
    },
  },
  mounted() {
    this.updateNow();
    this.timer = setInterval(this.updateNow, 10 * 1000);
  },
  beforeUnmount() {
    clearInterval(this.timer);
  },
};
</script>

<style scoped>
.large-text {
  font-size: 35px;
}

.stale {
  text-decoration: line-through;
  color: gray;
}

.class-muted {
  opacity: 0.75;
}
</style>
