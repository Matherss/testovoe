<template>
  <div class="card__wrapper">
    <div class="games">
      <div class="firstTeam">
        <img :src="logoHomeSrc" /><span class="firstTeamName">{{ teamHome }} ({{ teamHomeShortName }})</span>
      </div>
      <div class="scores">
        <span class="score">{{ scoreFtHome }}</span
        >:<span class="score">{{ scoreFtAway }}</span>
      </div>

      <div class="secondTeam">
        <span class="secondTeamName">{{ teamAway }} ({{ teamAwayShortName }})</span><img :src="logoAwaySrc" />
      </div>
    </div>
    <div class="matchInfo">
      <span v-if="stadium !== null">Стадион: {{ stadium.name }},</span> Дата: {{ actualDate }} | Тур: {{ tourNumber }} |
      ID: {{ _id }}
    </div>
  </div>
</template>

<script>
export default {
  name: "Card",
  props: {
    teamHome: String,
    teamHomeShortName: String,
    teamHomeLogo: String,
    teamHomeLogoId: Number,
    teamAway: String,
    teamAwayShortName: String,
    teamAwayLogo: String,
    scoreFtHome: Number,
    scoreFtAway: Number,
    _id: String,
    tourNumber: Number,
    stadium: Object,
    date: String
  },

  mounted() {
    console.log(this.teamHomeLogo);
    return {};
  },
  computed: {
    logoHomeSrc() {
      return `https://footballista.ru/api/img/logos/${this.teamHomeLogo}-middle.png?`;
    },
    logoAwaySrc() {
      return `https://footballista.ru/api/img/logos/${this.teamAwayLogo}-middle.png?`;
    },
    actualDate() {
      return this.date.match(/.{10}/).toString();
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.matchInfo {
  padding: 5px 0px 15px;
}
.games {
  display: flex;
  justify-content: center;
  text-align: center;
  margin: 0;
  transition: 0.5s all ease-in-out;
}
.games:hover {
  transform: translateY(5px);
}
.card__wrapper {
  padding: 5px;
}

.firstTeam,
.secondTeam {
  border: 1px solid black;
  width: 272px;
  padding: 10px;
  display: flex;
  align-items: center;
  justify-content: start;
  text-align: left;
}
.firstTeamName,
.secondTeamName {
  display: inline-block;
}
.scores {
  margin: 15px;
  display: flex;
  align-items: center;
}
.score {
  margin: 2px;
}
img {
  width: 60px;
  height: 60px;
}
</style>
