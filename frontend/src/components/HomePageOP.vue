<template>
  <v-main>
    <v-container fluid>
      <v-card min-height="650px">
        <v-layout row wrap ml-7 mr-8>
            <v-flex row wrap class="ma-3 mt-6">
              <v-btn @click="homepagepr" text>Requests</v-btn>
              <v-btn class="orange--text" text>In Progress</v-btn>
              <v-btn @click="homepagenr" text>Needs Review</v-btn>
              <v-btn @click="homepagecp" text>Completed</v-btn>
            </v-flex>
                  
                  <v-col class="text-right mt-3">
                    <v-menu offset-y>
                      <template v-slot:activator="{ on, attrs }">
                        <v-btn v-bind="attrs" v-on="on" rounded text>
                          Sort by 
                          <v-icon>mdi-chevron-down</v-icon>
                        </v-btn>
                      </template>
                      <v-list>
                        <v-list-item v-for="(item, index) in items" :key="index" link>
                          <v-list-item-title>{{ item.title }}</v-list-item-title>
                        </v-list-item>
                      </v-list>
                    </v-menu>
                  </v-col>
              </v-layout>

                    <v-layout row wrap ml-8>
                      <v-flex v-for="(item,e) in OnCards" :key="item.ID" class="custom">
                        <v-card 
                         class="ma-2" 
                         max-width="450" 
                         min-height="200"
                         max-height="200" 
                         outlined
                         :color="item.projectcardcolor"
                         @click="input(e)"
                         >
                          <v-list-item three-line>
                            <v-list-item-content>
                              <v-flex row wrap class="ma-0">
                            <p class="pt-2">Date created: {{ item.createdate }}</p>
                            <v-spacer />
                            <v-btn icon @click="item.IsPinned = !item.IsPinned">
                              <v-icon v-if="!item.IsPinned">mdi-pin-off</v-icon>
                              <v-icon v-if="item.IsPinned">mdi-pin</v-icon>
                            </v-btn>
                            <v-btn icon @click="item.IsFavourite = !item.IsFavourite">
                              <v-icon v-if="!item.IsFavourite">mdi-heart-outline</v-icon>
                              <v-icon v-if="item.IsFavourite">mdi-heart</v-icon>
                            </v-btn>
                              </v-flex>

                              <v-flex row wrap class="justify-center">
                            <h2>{{item.name}}</h2>
                          </v-flex>
                          <p>Progress</p>
                          <div class="pb-3">
                          <!-- <div class="pb-0 mb-0">
                          <v-flex row wrap>
                          <v-col cols="10" class="pb-0"> -->
                          <v-progress-linear
                            background-color="grey lighten-2"
                            color="red"
                            v-model="item.progress"
                            height="10"
                            />
                          <!-- </v-col> -->
                          <!-- <v-col cols="2" class="pb-0">
                          <p class="round1">{{item.progress + "%"}}</p>
                          </v-col> 
                          </v-flex> -->
                          </div>
                            <hr class="solid">
                          <v-flex row wrap class="pt-2">
                          <p class="pt-2 ml-4 underline">Member: {{ item.acceptedmembers.length }}</p>
                          <v-spacer/>
                          <p class="pt-2 round orange--text">{{ Math.ceil(parseInt((new Date(item.deadline)-new Date(item.createdate))/(24*3600*1000))) }} Days Left</p>
                          </v-flex>
                            </v-list-item-content>
                          </v-list-item>
                        </v-card>
                      </v-flex>
                    </v-layout>
                </v-card>
    </v-container>
  </v-main>
</template>

<script>
const axios = require('axios');
export default {
  name: "HomePageOP",
  title: "Home",
  data() {
    return {
      items: [
        { title: "Date: New to old" },
        { title: "Date: Old to new" },
        { title: "Progress: High to low" },
        { title: "Progress: Low to high" },
        { title: "Favourited" },
      ],
      OnCards: [],
    }
  },
  created(){
      axios
      .get('api/create/membersog', { 
        params:{
          acceptedmembers: window.$cookies.get("userid")
        }})
      .then((response) => {
          this.OnCards = response.data
          for(var i=0; i<this.OnCards.length; i++){
            var members = this.OnCards[i].inputtedmembers
            if(members == window.$cookies.get("userid"))
              this.OnCards[i].projectcardcolor = '#1DE9B6'
            else 
              this.OnCards[i].projectcardcolor = 'warning'
          }
      })
    },
  methods:{
    homepagepr(){
      this.$router.push({ name: "HomePagePR" });
    },
    homepagenr(){
    this.$router.push({ name: "HomePageNR"});
    },
    homepagecp(){
      this.$router.push({ name: "HomePageCP" })
    },
    input(e){
      let projectid = this.OnCards[e]._id
      window.$cookies.set("acceptedprojectid", projectid, Infinity)
      this.$router.push({ name: "DesDesignProject" })
    },
  }
};
</script>

<style scoped>
.flex.custom{
  flex-grow: 0;
  flex-shrink: 0;
  flex-basis: 500px;
}

.v-btn {
  margin-left: 10px;
}

p.round{
  border-radius: 8px;
  background-color: white;
  width: 100px;
  height: 30px;
  padding-left: 5px;
}

p.round1{
  border-radius: 8px;
  background-color: white;
  width: 50px;
  height: 20px;
  padding-left: 5px;
}
</style>
