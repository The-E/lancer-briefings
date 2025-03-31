<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
          <h3>Mission Structure</h3>
          <div class="mission-list-container">
            <Mission
              v-for="item in this.missions"
              :key="item.slug"
              :mission="item"
              :selected="this.mission_slug"
              @click="selectMission(item)"
            />
          </div>
        <h3>Phase Details</h3>
        <Markdown :source="current_md" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Situation Briefing</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
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
    style="visibility: hidden; position: absolute;"
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
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "001",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Welcome Back",
          "status": "success"
        },
      ],
      "pilots": [
        {
          "callsign": "Fuse",
          "alias": "Finlay Morris",
          "code": "this-is-calliope",
          "corpro": "HA",
          "frame": "Iskander",
          "mech": "Occupational Hazard"
        },
        {
          "callsign": "Snapshot",
          "alias": "Fan Bonsai",
          "code": "can-i-just-put-anything-here",
          "corpro": "SSC",
          "frame": "Emperor",
          "mech": "Line Goes Out"
        },
        {
          "callsign": "Athena",
          "alias": "Booze Cartridge",
          "code": "yes",
          "corpro": "SSC",
          "frame": "Oleander",
          "mech": "Olympus"
        },
        {
          "callsign": "Frost",
          "alias": "Robert Conway",
          "code": "look-to-windward",
          "corpro": "IPS-N",
          "frame": "Kidd",
          "mech": "A Closed And Common Orbit"
        },
        {
          "callsign": "",
          "alias": "",
          "code": "462370be-bd0f-41c2-b667-cc75f3a59a96",
          "corpro": "",
          "frame": "",
          "mech": ""
        },
      ],
      "header": {
        "planet": "Hell's Gate",
        "year": "5014u",
        "system": "Calliope",
        "gate": "Rào Cỏ",
        "ring": "Annamite",
        "headerTitle": "DRAGON'S TOOTH",
        "headerSubtitle": "HG-0451",
        "subheaderTitle": "Hell's Gate Militia",
        "subheaderSubtitle": "Strategic Response Team",
      },
      "options":{
        "eventsMarkdownPerMission": false
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `./missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `./events/${self.mission_slug}.md`
      }
      else {
        md = "./events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
