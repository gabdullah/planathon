<template>
<div class="auth-widget purple-gradient" id="register">
  <h2>Register</h2>
  <p>Email:</p>
  <input v-model="email" type="text" class="auth-textbox"/>
  <p>Password:</p>
  <input v-model="password" type="password" class="auth-textbox"/> <br><br>
  <button @click="signIn()" class="material-button-large ">Let's Go!</button> <br><br>
  
  <p class="err-text">{{err}}</p>
  
  <p>Already have an account?
    <router-link to="/login" tag="span" class="inline-link">
      Login!
    </router-link>
  </p>
</div>
</template>

<script>
import * as firebase from 'firebase';

export default {
    data() {
        return {
          email: "",
          password: "",
          err: ''
        }
    },
    methods: {
      signIn() {
        this.err = '';
        // Registering the user in the AUTH table:
        firebase.auth().createUserWithEmailAndPassword(this.email, this.password)
        .then((doc) => {
          console.log(doc.user);

          // Registering the user in the database table:
          this.$parent.db.collection('users').doc(doc.user.uid)
          .set({
            id: doc.user.uid,
            email: doc.user.email
          })
          // Redirecting to the main from /register:
          this.$router.push('/');
        }).catch((error) => {
          // Catching errors:
          this.err = error.message;
          console.error("Error signing you in!")
//            throw error;
        });
      }
    }
 }
</script>

<style scoped lang="scss">
@import '@/GlobalVars.scss';

  .material-button-large {
    background: $gray;
    color: white;
  }
</style>
