<template>
  <div class="wrapper">
    <input type="text" v-model="searchText" placeholder="Название команды" />
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
      searchText: ""
    };
  },
  beforeMount() {
    this.update();
  },
  methods: {
    update() {
      this.limit = this.limit + 10;
      axios({
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
      }).then((result) => {
        this.info = result.data;
        console.log(this.info);
      });
    }
  },
  computed: {
    filters() {
      let regexp = new RegExp(this.searchText, "i");
      return this.info.data.Season.calendar.items.filter(
        (item) => item.teamHome.name.match(regexp) || item.teamAway.name.match(regexp)
      );
    }
  }
};
</script>

<style>
* {
  box-sizing: border-box;
  margin: 0;
}
.wrapper {
  max-width: 1230px;
  margin: 0 auto;
}
.container {
  max-width: 1230px;
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
</style>
