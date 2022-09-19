<template>
  <v-container>
    <v-tabs v-model="selectedTab" color="yellow" align-with-title>
      <v-tabs-slider color="yellow"></v-tabs-slider>

      <v-tab v-for="item in items" :key="item">
        {{ item }}
      </v-tab>
    </v-tabs>
    <div class="mt-5 text-center" v-if="selectedTab === 1">
      <div v-if="haveFavoriteActor">
        <div
          v-for="(actor, index) in favoriteActorList"
          :key="index"
          class="parent d-inline-flex"
        >
          <div>
            <v-card width="250" class="text-center pa-4 ma-2">
              <div class="text-right">
                <v-btn
                  icon
                  :color="actor.favorite ? 'yellow' : ''"
                  @click="onClickFavorite(actor)"
                >
                  <v-icon>mdi-star</v-icon>
                </v-btn>
              </div>
              <v-avatar size="56">
                <img
                  alt="actor"
                  v-if="actor.image && actor.image.medium && actor.image.medium"
                  :src="actor.image && actor.image.medium && actor.image.medium"
                />
                <img
                  alt="actor"
                  v-else
                  src="https://cdn.pixabay.com/photo/2020/07/12/07/47/bee-5396362_1280.jpg"
                />
              </v-avatar>
              <div class="mt-3 font-weight-bold">{{ actor.name }}</div>
              <div class="mt-3">
                {{ actor.birthday ? actor.birthday : "No brith date" }}
              </div>
              <div class="mt-3" v-if="actor.country">
                {{ actor.country && actor.country.name }}
              </div>
              <div class="mt-3" v-else>No country name</div>
            </v-card>
          </div>
        </div>
      </div>
      <div v-else>No Favorite Actor</div>
    </div>
    <div class="mt-5 text-center" v-if="selectedTab === 0">
      <div v-for="(actor, index) in actorList" :key="index" class="parent d-inline-flex">
        <div>
          <v-card width="250" class="text-center pa-4 ma-2">
            <div class="text-right">
              <v-btn
                icon
                :color="actor.favorite ? 'yellow' : ''"
                @click="onClickFavorite(actor)"
              >
                <v-icon>mdi-star</v-icon>
              </v-btn>
            </div>
            <v-avatar size="56">
              <img
                alt="actor"
                v-if="actor.image && actor.image.medium && actor.image.medium"
                :src="actor.image && actor.image.medium && actor.image.medium"
              />
              <img
                alt="actor"
                v-else
                src="https://cdn.pixabay.com/photo/2020/07/12/07/47/bee-5396362_1280.jpg"
              />
            </v-avatar>
            <div class="mt-3 font-weight-bold">{{ actor.name }}</div>
            <div class="mt-3">
              {{ actor.birthday ? actor.birthday : "No brith date" }}
            </div>
            <div class="mt-3" v-if="actor.country">
              {{ actor.country && actor.country.name }}
            </div>
            <div class="mt-3" v-else>No country name</div>
          </v-card>
        </div>
      </div>
    </div>
  </v-container>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",

  data: () => ({
    selectedTab: 0,
    items: ["actors", "favorites"],
    actorList: [],
    favoriteActorList: [],
  }),
  created() {
    localStorage.setItem("FavoriteActorList", []);
    this.getActors();
  },
  computed: {
    haveFavoriteActor() {
      return JSON.parse(localStorage.getItem("FavoriteActorList"));
    },
  },
  methods: {
    getActors() {
      axios.get("https://api.tvmaze.com/people").then((res) => {
        this.actorList = res.data.map((element) => {
          element.favorite = false;
          return element;
        });
      });
    },
    onClickFavorite(actor) {
      if (actor.favorite) {
        actor.favorite = false;
        if (this.favoriteActorList.length > 0) {
          const Findactor = this.favoriteActorList.find((a) => a.id === actor.id);
          if (Findactor) {
            let FindIndex = null;
            this.favoriteActorList.forEach((a, index) => {
              if (a.id === actor.id) {
                FindIndex = index;
              }
            });
            this.favoriteActorList.splice(FindIndex, 1);
            localStorage.removeItem('FavoriteActorList')
            localStorage.setItem(
              "FavoriteActorList",
              JSON.stringify(this.favoriteActorList)
            );
          }
        }
      } else {
        actor.favorite = true;
        if (this.favoriteActorList.length > 0) {
          const Findactor = this.favoriteActorList.find((a) => a.id === actor.id);
          if (!Findactor) {
            this.favoriteActorList.push(actor);
            localStorage.removeItem('FavoriteActorList')
            localStorage.setItem(
              "FavoriteActorList",
              JSON.stringify(this.favoriteActorList)
            );
          }
        } else {
          this.favoriteActorList.push(actor);
          localStorage.removeItem('FavoriteActorList')
          localStorage.setItem(
            "FavoriteActorList",
            JSON.stringify(this.favoriteActorList)
          );
        }
      }
    },
  },
};
</script>
<style>
.parent {
  display: flex;
  flex-wrap: wrap;
}
</style>
