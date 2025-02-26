<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section
      class="section-container"
      id="missions"
      style="width: 435px; height: 714px"
    >
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section
      class="section-container"
      id="events"
      style="width: 435px; height: 714px"
    >
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Mission Briefing</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section
      class="section-container"
      id="pilots"
      style="width: 894px; height: 714px"
    >
      <div style="height: 52px; overflow: hidden">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer />
</template>

<script>
import Header from "./components/layout/Header.vue";
import Footer from "./components/layout/Footer.vue";
import Mission from "./components/Mission.vue";
import Pilot from "./components/Pilot.vue";
import Markdown from "vue3-markdown-it";

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown,
  },

  data() {
    return {
      mission_slug: "003",
      current_md: "",
      events: "",
      missions: [
        {
          slug: "001",
          name: "OPERATION?CARDHOLDER",
          status: "success",
        },
        {
          slug: "002",
          name: "OPERATION?ASTEROID HOPPING",
          status: "success",
        },
        {
          slug: "003",
          name: "OPERATION?ALMOST HUMAN",
          status: "start",
        },
      ],
      pilots: [
        {
          callsign: "OMEN",
          alias: "Zacharias Morrison",
          code: "Morrison.Zacharias:3abcea30-311a-4c23-8425-09837aaaecbf//NDL-C-BRONZE-HAMMER",
          corpro: "SSC",
          frame: "Monarch",
          mech: "Brilliant Raptor",
        },
        {
          callsign: "THORN",
          alias: "Ferrous Foss",
          code: "Foss.Ferrous:3a822132-785b-4f68-9d6a-32cdd70baf23//NDL-C-UNBENT-CRYPT",
          corpro: "IPS-N",
          frame: "Blackbeard",
          mech: "Conjoined Torment",
        },
        {
          callsign: "STARCHILD",
          alias: "Jesk Oranzo",
          code: "Oranzo.Jesk:4ede1e58-01c8-498d-a1f2-5e4fc3b93435//NDL-C-NULL-TEMPLE",
          corpro: "HORUS",
          frame: "Gorgon",
          mech: "Last Vision",
        },
        {
          callsign: "PROPHET",
          alias: "Greva Banard",
          code: "Banard.Greva:98ca9616-044e-4f87-b89b-aae4eb3387ec//NDL-C-DEEP-STATION",
          corpro: "HA",
          frame: "Barbarossa",
          mech: "MACHINE GOD",
        },
      ],
      header: {
        planet: "Otionip",
        year: "5012u",
        system: "Peles",
        gate: "Karakoram-Gasherbrum",
        ring: "Karakoram-Line",
        headerTitle: "STAR*SEA",
        headerSubtitle: "HI-SECURITY",
        subheaderTitle: "High Security",
        subheaderSubtitle: "Intelligence & Operations",
      },
      options: {
        eventsMarkdownPerMission: true,
      },
    };
  },

  created() {
    this.loadMissionMarkdown();
    this.loadEventsMarkdown();
  },

  computed: {},

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown();
      if (this.options.eventsMarkdownPerMission) {
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`;
      var client = new XMLHttpRequest();
      client.open("GET", md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      };
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if (self.options.eventsMarkdownPerMission) {
        md = `/events/${self.mission_slug}.md`;
      } else {
        md = "/events.md";
      }

      var client = new XMLHttpRequest();
      client.open("GET", md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      };
      client.send();
    },
  },
};
</script>

<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
