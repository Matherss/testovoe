<template>
  <div v-if="loading">
    <h2>Page Loading...Please Wait</h2>
  </div>
  <div v-if="info" class="wrapper">
    <div v-for="(team, idx) of teams" :key="idx">
      <input
        type="checkbox"
        :id="team"
        :key="idx"
        :value="team"
        v-model="checkedNames"
        :disabled="checkedNames.length == 2 && checkedNames.indexOf(team) === -1"
      />
      <label :for="team">{{ team }}</label>
    </div>
    <div class="container">
      <card
        v-for="(item, index) of filters"
        :key="index"
        :teamHome="item.teamHome.name"
        :teamHomeShortName="item.teamHome.shortName"
        :teamHomeLogo="item.teamHome.logo"
        :teamHomeLogoId="item.teamHome.logoId"
        :teamAway="item.teamAway.name"
        :teamAwayShortName="item.teamAway.shortName"
        :teamAwayLogo="item.teamAway.logo"
        :scoreFtHome="item.scoreFtHome"
        :scoreFtAway="item.scoreFtAway"
        :_id="item._id"
        :tourNumber="item.tourNumber"
        :stadium="item.stadium"
        :date="item.date"
      >
      </card>
    </div>
    <button @click="update()">Загрузить ещё</button>
  </div>
</template>

<script>
import axios from "axios";
import Card from "./components/Card.vue";

export default {
  name: "App",
  components: {
    Card
  },
  data() {
    return {
      info: null,
      limit: 0,
      loading: true,
      teams: [],
      checkedNames: []
    };
  },
  async beforeMount() {
    await this.update();
  },
  methods: {
    async update() {
      this.loading = true;
      this.limit += 10;
      await axios({
        url: "https://footballista.ru/graphql",
        method: "post",
        data: {
          query: `{
 Season(_id: 5099) {
  calendar(offset:0, limit:${this.limit}) {
    total, items {
      _id
      tourNumber
      teamHome {name, shortName, logo, logoId }
      teamAway {name, shortName, logo, logoId }
      scoreFtHome
      scoreFtAway
      stadium { name }
      date
    }
  }
 }}`
        }
      })
        .then((result) => {
          this.info = result.data;
          const teamsmap = this.info.data.Season.calendar.items.reduce((acc, game) => {
            return { ...acc, ...{ [game.teamHome.name]: game.teamHome.name } };
          }, {});

          // for (let i = 0; i < this.info.data.Season.calendar.items.length; i++) {
          //   // if (this.teams.indexOf(this.info.data.Season.calendar.items[i].teamHome.name) == -1)
          //   // this.teams.push(this.info.data.Season.calendar.items[i].teamHome.name);

          //     teamsmap[this.info.data.Season.calendar.items[i].teamHome.name] = this.info.data.Season.calendar.items[i].teamHome.name;

          // }
          this.teams = Object.values(teamsmap);

          for (let i = 0; i < this.info.data.Season.calendar.items.length; i++) {
            if (this.teams.indexOf(this.info.data.Season.calendar.items[i].teamAway.name) !== -1) continue;
            this.teams.push(this.info.data.Season.calendar.items[i].teamAway.name);
          }
          console.log(this.teams);
          console.log(result.data);
        })
        .catch((err) => {
          console.log("ERR" + err);
        })
        .finally(() => (this.loading = false));
    }
  },
  computed: {
    filters() {
      console.log(this.checkedNames);
      const { items } = this.info.data.Season.calendar;
      if (this.checkedNames) {
        console.log(this.checkedNames);
        let regex = new RegExp(this.checkedNames[0], "i");
        let regex2 = new RegExp(this.checkedNames[1], "i");
        return items
          .filter((item) => item.teamHome.name.match(regex) || item.teamAway.name.match(regex))
          .filter((item) => item.teamHome.name.match(regex2) || item.teamAway.name.match(regex2));
      }
      return items;
    }
  }
};
</script>

<style lang="scss">
:root {
  --border-color: green;
}
@mixin max_width {
  max-width: 1230px;
}
* {
  box-sizing: border-box;
  margin: 0;
}
.wrapper {
  @include max_width;
  margin: 0 auto;
}
.container {
  @include max_width;
  display: grid;
  grid-template-columns: 1fr 1fr;
  justify-items: center;
  grid-gap: 2px;
}

@media screen and (max-width: 767px) {
  .container {
    grid-template-columns: 1fr;
  }
}

button {
  background-color: initial;
  background-image: linear-gradient(#8614f8 0, #760be0 100%);
  border-radius: 5px;
  border-style: none;
  box-shadow: rgba(245, 244, 247, 0.25) 0 1px 1px inset;
  color: #fff;
  cursor: pointer;
  display: flex;
  justify-content: center;
  font-family: Inter, sans-serif;
  font-size: 16px;
  font-weight: 500;
  height: 60px;
  line-height: 60px;
  margin-left: 15px;
  outline: 0;
  text-align: center;
  transition: all 0.3s cubic-bezier(0.05, 0.03, 0.35, 1);
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
  vertical-align: bottom;
  width: 190px;
}

button:hover {
  opacity: 0.7;
}

@media screen and (max-width: 1000px) {
  button {
    font-size: 14px;
    height: 55px;
    line-height: 55px;
    width: 150px;
  }
}
input[type="checkbox"]:disabled {
  border: 1px solid var(--border-color);
}
</style>
