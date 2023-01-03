<template>
    <div class="list">
    <article v-for="(pokemon, index) in pokemons" :key="'poke'+index" @click="setPokemonUrl(pokemon['url'])">
      <h3>#{{pokemon['id'] + " " + pokemon['name'] }}</h3>
    </article>
    <div id="scroll-trigger" ref="infinitescrolltrigger">
      <img class="loading-spinner" src="../assets/loading-spinner-white.gif">
    </div>
  </div>
</template>

<script lang="ts">
export default {
  props: [
    'apiUrl'
  ],
  data: () => {
    return {
      pokemons: [],
      nextUrl: '',
      currentUrl: ''
    }
  },
  methods: {
    fetchData() {
      let req = new Request(this.currentUrl);
      fetch(req)
        .then((resp) => {
          if(resp.status === 200)
            return resp.json();
        })
        .then((data) => {
          this.nextUrl = data.next;
          data.results.forEach((pokemon: { id: any; url: string; }) => {
            pokemon.id = pokemon.url.split('/')
              .filter(function(part: any) { return !!part }).pop();
            this.pokemons.push(pokemon);
          });
        })
        .catch((error) => {
          console.log(error);
        })
    },
    scrollTrigger() {
      const observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
          if(entry.intersectionRatio > 0 && this.nextUrl) {
            this.next();
          }
        });
      });
      observer.observe(this.$refs.infinitescrolltrigger);
    },
    next() {
      this.currentUrl = this.nextUrl;
      this.fetchData();
    },
    setPokemonUrl(url: any) {
      this.$emit('setPokemonUrl', url);
    }
  },
  created() {
    this.currentUrl = this.apiUrl;
    this.fetchData();
  },
  mounted() {
    this.scrollTrigger();
  }
}
</script>

<style lang="scss" scoped>
.list {
    display: grid;
    grid-gap: 2px;
    width: 100%;
    article {
        height: 35px;
        text-align: left;
        text-transform: capitalize;
        color: white;
        font-size: 1.5em;
        border-radius: 5px;
        background-color: rgba(0, 0, 0, 0);
        cursor: pointer;
        h3 {
            margin-left: 0.5em;
        }
    }

    article:hover{
        background-color: rgba(0, 0, 0, 0.5);
    }
    
}

#scroll-trigger {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 35px;
  font-size: 1.5em;
  color: #efefef;
  .loading-spinner{
    height: 35px;
    width:  35px;
  }
}
</style>