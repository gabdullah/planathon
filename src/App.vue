<template>
  <div id="app" class="full-page">
    <menu-bar :user="user"></menu-bar>
    <router-view/>
  </div>
</template>

<script>
import config from '@/config/config.js';
import * as firebase from 'firebase';
import VueFire from 'vuefire';
import Vue from 'vue';

import MenuBar from '@/components/MenuBars/LandingMenuBar.vue';

firebase.initializeApp(config);
var db = firebase.firestore();
const settings = {/* your settings... */ timestampsInSnapshots: true};
db.settings(settings);

export default {
  data() {
    return {
      db: db,
      user: null,
      userOrgs: [],
      loadingUser: true,
      org: null,
      hackathon: null
    }
  },
  components: {
    MenuBar
  },
  methods: {
    loadUser(id) {
      // Getting the user's info from the User table (which is different from the Auth table!)
      this.db.collection('users').doc(id)
      .get().then((doc) => {
        this.user = doc.data();
        // Loading in the user's orgs now that we have the IDs from the user's table
        this.loadOrgs();
        this.loadingUser = false;
      }).catch((err) => {
        console.error("Error getting the user's information: ", err);
      })
    },
    loadOrgs() {
      // Getting the data from each of the orgs based on the org's ID
      // (which was stored in the User's table)
      for (let id in this.user.orgs) {
        console.log("Hm.", this.user.orgs[id])
        this.db.collection('orgs').doc(id)
        .get().then((doc) => {
          // console.log(doc.data());
          var org = doc.data();
          this.userOrgs.push(org);
          for (var i in org.hackathons) {
            this.loadHackathon(this.userOrgs.length - 1, org.hackathons[i].id);
          }
          //console.log(doc.data());
        }).catch((err) => {
          console.error("Error in loadOrgs with the org ID " + id + ":", err);
        })
      }
    },
    loadHackathon(orgId, hackathonId) {
      console.warn(orgId, hackathonId);
      this.db.collection('hackathons').doc(hackathonId).get()
      .then((doc) => {
        console.log(doc.data());
        console.log(this.userOrgs[orgId])
        this.userOrgs[orgId].hackathons[hackathonId] = doc.data();
      })
    },
    logout() {
      firebase.auth().signOut();
      this.user = null;
    },
  },
  mounted() {

    firebase.auth().onAuthStateChanged((user) => {
      // This if statement will be true if the user is logged in
      // when the page loads! :)
      if (user){
        this.loadUser(user.uid);
        this.loggedIn = true;
        this.loginModule = false;
      } else {
        this.loadingUser = false;
      }
    });

  }
}

</script>

<style lang="scss">

@import '@/GlobalVars.scss';

  .full-page {
    position: absolute;
    left: 0px;
    top: 0px;
    width: 100%;
    min-height: 100%;
  }

  .yellow {
    color: $yellow;
  }
  .light-yellow {
    color: $light-yellow
  }
  .orange {
    color: $orange;
  }
  .light-orange {
    color: $light-orange;
  }
  .pink {
    color: $pink;
  }
  .light-pink {
    color: $light-pink;
  }
  .purple {
    color: $purple;
  }
  .light-purple {
    color: $light-purple;
  }
  .blue {
    color: $blue;
  }
  .light-blue {
    color: $light-blue;
  }

  .finance {
    background-color: $yellow;
  }
  .development {
    background-color: $orange;
  }
  .promotion {
    background-color: $pink;
  }
  .design {
    background-color: $purple;
  }
  .general {
    background-color: $blue;
  }

  .link {
    cursor: pointer;
  }
  .faded-link {
    opacity: .5;
    cursor: pointer;
  }
  .faded-link:hover {
    opacity: 1;
  }

  .dark-gray-widget {
    background: $dark-gray;
    padding: 10px 20px;
    color: white;
    box-shadow: $box-shading;
  }

  .bold {
    font-weight: bolder;
  }

  .material-button-large {
    padding: 15px 50px;
    box-shadow: $box-shading;
    font-weight: bold;
    border: none;
    font-size: 20px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
    transition: 200ms ease all;


    &:before {
      content: '';
      position: absolute;
      top: -40%;
      right: 150%;
      width: 30px;
      height: 200%;
      background: rgba(white, .3);
      transform: rotate(20deg);
    }

    &:hover {

      &:before {
        right: -50%;
        transition: 1s ease all;
      }
    }

  }
  .purple-gradient {
    background: $purple-gradient;
    color: white;
  }
  .orange-gradient {
    background: $orange-gradient;
    color: white;
  }

  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: white;
    background: $light-gray;
  }
  

/*  --------------- */
/*  POPUP STYLING: */
/*  --------------- */

  .popup-background {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, .5);
    display: table;
    transition: opacity .4s ease;
  }

  .popup-wrapper {
    display: table-cell;
    vertical-align: middle;
  }

  .popup-table{
    width: 400px;
    margin: 0 auto;
    padding: 20px;
    margin-top: 30px;
    box-shadow: $box-shading;
  }

/*  ------------- */
/*  AUTH STYLING: */
/*  ------------- */

  .auth-widget {
    width: 400px;
    margin: 0 auto;
    padding: 20px;
    margin-top: 30px;
    box-shadow: $box-shading;
  }

  .err-text {
    color: white;
    text-decoration: underline;
    text-decoration-color: $orange;
  }

  .inline-link {
    text-decoration: underline;
    cursor: pointer;
  }

  .auth-textbox {
    border: solid $light-gray 1px;
    background: rgba(0,0,0,.1);
    padding: 5px;
    color: white;
    font-size: 16px;
  }

</style>

<style scoped lang="scss">

</style>
