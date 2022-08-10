<template>
  <Header :header="this.header" />
  <div class="content-container">
    <MissionView
      :missions="this.missions"
      :missionMarkdown="this.current_md"
      :selected="this.mission_slug"
      v-on:missionSelected="handleMissionSelected"
    />

    <EventsView :events="this.events" />
    <section class="section-container" id="main-tab">
      <div class="main-tab-header" style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img :src="mainTabIcon" />
          <h1>{{ mainTabTitle }}</h1>
        </div>
        <TabButton
          v-for="item in this.options.panelOptions"
          :name="item"
          :hidden="item === this.options.mainPanel"
          :key="item"
          @click="selectMainPanel(item)"
        />
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <transition name="fade" mode="out-in">
          <PilotsView v-if="this.options.mainPanel === 'pilot'" :pilots="this.pilots" />
          <NPCView v-else-if="this.options.mainPanel === 'npc'" :npcs="this.npcs" />
          <GlossaryView v-else-if="this.options.mainPanel === 'glossary'" />
          <ClocksView v-else-if="this.options.mainPanel === 'clock'" :clocks="this.clocks" />
        </transition>
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
  <Footer />
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import MissionView from './components/layout/MissionView.vue';
import EventsView from './components/layout/EventsView.vue';
import PilotsView from './components/layout/PilotsView.vue';
import NPCView from './components/layout/NPCView.vue';
import ClocksView from './components/layout/ClocksView.vue';
import GlossaryView from './components/layout/GlossaryView.vue';
import TabButton from './components/TabButton.vue'

export default {
  components: {
    Header,
    Footer,
    MissionView,
    EventsView,
    PilotsView,
    NPCView,
    GlossaryView,
    ClocksView,
    TabButton,
  },

  data() {
    return {
      "mission_slug": "001",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Bug-Hunt",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "King",
          "alias": "Rey Rodriguez",
          "code": "Rodriguez.Rey:426d13ce-c336-48ed-8f7a-05adec4fda91//NDL-C-SATELLITE-VEIL",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Oscuro"
        },
        {
          "callsign": "OsKILLoscope",
          "alias": "Eutropio Eshbaugh",
          "code": "Eshbaugh.Eutropio:165eac96-e791-4cb3-934e-018475e9135f//NDL-C-SECOND-TELLURION",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "The Anxiety Mechanism"
        },
        {
          "callsign": "Goldleaf",
          "alias": "Ormander Kellman",
          "code": "Kellman.Ormander:653de6e9-acff-4ff1-bbd9-e62a84219377//NDL-C-DISCORDANT-CRYSTAL",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Ad Aspera"
        },
        {
          "callsign": "ST-2-01",
          "alias": "Alex Thompson",
          "code": "Thompson.Alex:72457e6a-9b36-45db-9530-2a9e9fcd47e2//NDL-C-BRASS-MANTLE",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Cait Sith"
        },
        {
          "callsign": "Xiv",
          "alias": "Xiv",
          "code": "XIV:6e96da55-f522-478c-af2f-de9c89276eb0//NDL-C-BLUE-HIDE",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Frank"
        },
      ],
      "npcs": [
        {
          "name": "Brava Hadura",
          "affiliation": "Evergreen",
          "pronouns": "She/Her",
          "notes": "Captain of the Local Militia"
        },
        {
          "name": "Patience",
          "affiliation": "Evergreen",
          "pronouns": "They/Them",
          "notes": "Colonial Administrator"
        },
        {
          "name": "Edena Ji",
          "affiliation": "Evergreen",
          "pronouns": "She/Her",
          "notes": "Chief Operations Officer, Attaché to Patience"
        },
        {
          "name": "Castor Fielding",
          "affiliation": "Evergreen",
          "pronouns": "He/Him",
          "notes": "Chief Engineer"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Landmark Colonial",
        "headerSubtitle": "SSC-Subsidiary",
        "subheaderTitle": "Crisis Response Team",
        "subheaderSubtitle": "Recoup Blue",
      },
      "clocks": [
        {
          "name": "Defense of Evergreen",
          "description": "Represents the integrity and readiness of Evergreen's militia and defenses.",
          "help": "Having more segments filled in will make things easier for the Lancers during later missions.",
          "color": "#7DBBBB",
          "value": 0,
          "max": 6,
        },
      ],
      "options": {
        "eventsMarkdownPerMission": true,
        "mainPanel": "pilot",
        "panelOptions": [
          "pilot",
          "npc",
          "glossary",
          "clock"
        ]
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {
    mainTabTitle() {
      if (this.options.mainPanel === "pilot") return "Pilot Roster"
      if (this.options.mainPanel === "npc") return "Persons Registry"
      if (this.options.mainPanel === "glossary") return "Lexicon"
      if (this.options.mainPanel === "clock") return "Clocks"
    },
    mainTabIcon() {
      return `/icons/${this.options.mainPanel}-icon.svg`
    }
  },

  methods: {
    handleMissionSelected(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if (this.options.eventsMarkdownPerMission) {
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
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

      if (self.options.eventsMarkdownPerMission) {
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    },
    selectMainPanel(panel) {
      this.options.mainPanel = panel;
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
