<template>
  <div class="detail">
    <div class="detail-view" v-if="show">
      <div v-if="pokemon">
        <figure class="image">
          <img class="bg" :src="pokemon.bgPoke" alt="">
          <img class="poke" :src="pokemon.image" alt="">
        </figure>
      </div>
      <div v-if="pokemon" class="data">
        <h2>{{ pokemon.name }}</h2>
        <h5>CP{{
          Math.round((((2 * Math.round(Math.pow(pokemon.stats[1].attack!, 0.5) *
            Math.pow(pokemon.stats[3].special_attack!, 0.5) + Math.pow(pokemon.stats[5].speed!, 0.5))) +
            pokemon.stats[1].attack!) * /*BaseAtk + Atk*/
            Math.pow(((2 * Math.round(Math.pow(pokemon.stats[2].defense!, 0.5) *
              Math.pow(pokemon.stats[4].special_defense!,
                0.5) + Math.pow(pokemon.stats[5].speed!, 0.5))) + pokemon.stats[2].defense!), 0.5) * /*BaseDef + Def*/
            Math.pow(((2 * pokemon.stats[0].hp!) + pokemon.stats[0].hp!), 0.5) * /*BaseSta + Sta*/
            Math.pow((pokemon.xp / 20), 0.5)) / 100) /*Xp*/
        }}</h5>
        <div class="property">
          <div class="prop-column">
            <h3>{{ pokemon.weight / 10 }} kg</h3>
            <h4>weight</h4>
          </div>

          <div class="prop-column-mid" style="border-left: 1px solid #ccc; border-right: 1px solid #ccc;">
            <div v-if="pokemon.types.length > 1">
              <div class="prop-type">
                <figure class="img-type">
                  <img class="prop-top" :src="pokemon.imgType[0]" />
                  <img class="prop-top" :src="pokemon.imgType[1]" />
                </figure>
                <h4>{{ pokemon.types[0] }} / {{ pokemon.types[1] }}</h4>
              </div>
            </div>
            <div v-else>
              <figure class="img-type">
                <img class="prop-top" :src="pokemon.imgType[0]">
              </figure>
              <h4>{{ pokemon.types[0] }}</h4>
            </div>
          </div>

          <div class="prop-column">
            <h3>{{ pokemon.height / 10 }} m</h3>
            <h4>height</h4>
          </div>
        </div>
        <h3>Stats</h3>
        <div class="stats">
          <div class="stats-row" v-for="(poke, index) in pokemon.stats" :key="'poke' + index">
            <div class="stats-view" v-for="(value, key) in poke" :key="key">
              <div class="stats-label">
                <h4>{{ key.replace('_', ' ') }}</h4>
              </div>
              <div class="stats-info">
                <h4>{{ value }}</h4>
              </div>
              <div class="stats-bar">
                <v-progress-linear v-model="pokemon.statsBar[index]" color="light-blue" height="10" striped
                  rounded></v-progress-linear>
              </div>
            </div>
          </div>
        </div>
        <!--<h3>Abilities</h3>
        <div class="abilities">
          <div class="ability"
            v-for="(value, index) in pokemon.abilities"
            :key="'value'+index">
            {{ value.ability.name }}
          </div>
        </div>-->
      </div>
      <h2 v-else>The pokemon was not found</h2>
    </div>
    <img v-else src="../assets/loading-spinner-black.gif">
  </div>
</template>

<script lang="ts">
export default {
  props: [
    'pokemonUrl'
  ],
  data: () => {
    return {
      show: false,
      pokemon: {
        name: '',
        abilities: [],
        xp: 0,
        height: 0,
        image: '',
        stats: [
          { hp: 0 },
          { attack: 0 },
          { defense: 0 },
          { special_attack: 0 },
          { special_defense: 0 },
          { speed: 0 }
        ],
        statsBar: [],
        types: [],
        weight: 0,
        bgPoke: '',
        imgType: []
      }
    }
  },
  methods: {
    fetchData() {
      let req = new Request(this.pokemonUrl);
      fetch(req)
        .then((resp) => {
          if (resp.status === 200)
            return resp.json();
        })
        .then((data) => {
          this.show = true;
          this.pokemon.name = data.name;
          this.pokemon.abilities = data.abilities;
          this.pokemon.xp = data.base_experience;
          this.pokemon.height = data.height;
          this.pokemon.image = data.sprites.other.home.front_default;
          this.pokemon.stats = [
            { "hp": data.stats[0].base_stat },
            { "attack": data.stats[1].base_stat },
            { "defense": data.stats[2].base_stat },
            { "special_attack": data.stats[3].base_stat },
            { "special_defense": data.stats[4].base_stat },
            { "speed": data.stats[5].base_stat }
          ];
          this.pokemon.statsBar = [ //Max values based in gen 8th -> https://bulbapedia.bulbagarden.net/wiki/List_of_Pok%C3%A9mon_by_base_stats_(Generation_VIII-present)
            data.stats[0].base_stat * 100 / 255, //Max Hp
            data.stats[1].base_stat * 100 / 190, //Max Attack
            data.stats[2].base_stat * 100 / 250, //Max Defense
            data.stats[3].base_stat * 100 / 194, //Max Special Attack
            data.stats[4].base_stat * 100 / 250, //Max Special Defense
            data.stats[5].base_stat * 100 / 200  //Max Speed
          ];
          this.pokemon.weight = data.weight;
          this.pokemon.bgPoke = "../../src/assets/bg_types/" + data.types[0].type.name + ".png";
          data.types.forEach((types: { type: { name: string; }; }) => {
            this.pokemon.types.push(types.type.name);
            this.pokemon.imgType.push("../../src/assets/poke_types/" + types.type.name + ".png");
          })
        })
        .catch((error) => {
          console.log(error);
        })
    }
  },
  created() {
    this.fetchData();
  }
}
</script>

<style lang="scss" scoped>
.detail {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  position: absolute;
  top: 150px;
  width: calc(100% - 20%);
  border-radius: 5px;

  .detail-view {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    position: relative;
    height: 100%;
    width: 100%;
    max-width: 310px;
    background-color: #fff;

    .image {
      display: flex;
      justify-content: center;
      margin-top: -150px;
      position: relative;
      width: 300px;
      height: 300px;

      .bg {
        max-width: 100%;
        max-height: 100%;
      }

      .poke {
        position: absolute;
        max-width: 90%;
        max-height: 90%;
        top: 20%;
      }
    }

    .data {
      display: flex;
      justify-content: flex-start;
      align-items: center;
      flex-direction: column;
      width: 100%;
      height: 400px;
      margin-top: 30px;

      .property {
        display: flex;
        flex-flow: row wrap;
        width: 90%;
        height: 65px;
        margin-top: 20px;
        margin-bottom: 10px;
        border-bottom: 1px solid #ccc;

        .prop-column {
          display: flex;
          flex-flow: column;
          align-items: center;
          width: 30%;
          padding-top: 5px;
          margin-bottom: 10px;
        }

        .prop-column-mid {
          display: flex;
          flex-flow: column;
          align-items: center;
          justify-content: center;
          width: 40%;
          padding-top: 5px;
          margin-bottom: 10px;
        }

        .img-type {
          display: flex;
          justify-content: center;
          flex-flow: row;

          .prop-top {
            max-height: 30px;
            margin-left: 5px;
            margin-right: 5px;
          }
        }
      }

      .stats {
        display: flex;
        flex-flow: row wrap;
        width: 90%;
        height: 100%;
        margin-top: 10px;
        margin-bottom: 10px;
        border-bottom: 1px solid #ccc;

        .stats-row {
          display: flex;
          flex-flow: row;
          width: 100%;
          border-bottom: 1px solid #ccc;

          .stats-view {
            display: flex;
            align-items: center;
            width: 100%;
            height: 30px;
            margin-top: 2px;
            margin-bottom: 2px;

            .stats-label {
              display: flex;
              flex-flow: row;
              align-items: center;
              justify-content: center;
              width: 20%;
            }

            .stats-info {
              display: flex;
              flex-flow: column;
              align-items: flex-end;
              justify-content: right;
              width: 15%;
            }

            .stats-bar {
              display: flex;
              flex-flow: column;
              align-items: center;
              margin-left: 10px;
              width: 60%;
            }
          }
        }
      }

      h2 {
        text-transform: capitalize;
        font-weight: bold;
        font-size: 2em;
      }

      h3 {
        font-weight: bold;
        font-size: 1.1em;
        height: 30px;
      }

      h4 {
        text-transform: uppercase;
        font-weight: bold;
        font-size: 0.7em;
        text-align: center;
      }

      h5 {
        font-weight: bold;
        font-size: 0.8em;
      }
    }
  }

  i {
    font-size: 2rem;
    color: #efefef;
  }
}
</style>