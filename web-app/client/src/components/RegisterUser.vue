<template>
  <div class="posts">
        <p><router-link to="/">Home</router-link>&nbsp;</p>

    <br>
    <h3>Don't have a healthcare account? Sign up below</h3>
    <form v-on:submit.prevent="RegisterUser">
      <input type="text" v-model="registerData.email" placeholder="email"><br>
      <input type="password" v-model="registerData.password" placeholder="password">
      <br>
      <input type="password" v-model="registerData.confirmPass" placeholder="confirmPassword" >
      <br>
      <input type="text" v-model="registerData.lastName" placeholder="lastname">
      <br>
      <select v-model="registerData.mspid" name="mspid">
        <option  selected disabled> 
          Select an Option 
      </option>
        <option value="patientmsp" selected>Patient</option>
        <option value="manufacturermsp">Manufacturer</option>
        <option value="pharmacymsp">Pharmacy</option>
        <option value="w1msp">Wholesaler1</option>
        <option value="w2msp">Wholesaler2</option>
      </select>
      <br>
      <input type="submit" value="Sign up">
    </form>
    <br>
    <span v-if="registerReponse">
      <b>{{ registerReponse.data }}</b>
    </span>
    <br>
    <vue-instant-loading-spinner id='loader' ref="Spinner"></vue-instant-loading-spinner>
  </div>
</template>

<script>
import PostsService from "@/services/apiService";
import VueInstantLoadingSpinner from "vue-instant-loading-spinner/src/components/VueInstantLoadingSpinner.vue";

export default {
  name: "response",
  data() {
    return {
      registerData: {},
      registerReponse: {
        data: ""
      }
    };
  },
  components: {
    VueInstantLoadingSpinner
  },
  methods: {
    async RegisterUser() {

      console.log(this.registerData.password)
      console.log(this.registerData.confirmPass)

      if (this.registerData.password != this.registerData.confirmPass) {
        console.log('Your password does not match. Please enter matching');
        let response = 'Your password does not match. Please enter matching passwords.';
        this.registerReponse.data = response;
        await this.hideSpinner(); 
      } else {
        this.registerReponse = '';
        await this.runSpinner();
        console.log('before register USER')
        const apiResponse = await PostsService.RegisterUser(
          this.registerData.email,
          this.registerData.password,
          this.registerData.confirmPass,
          this.registerData.lastName,
          this.registerData.mspid        
        );

        console.log(apiResponse);
        this.registerReponse = apiResponse;
        await this.hideSpinner();
      }
    },
    async runSpinner() {
      this.$refs.Spinner.show();
    },
    async hideSpinner() {
      this.$refs.Spinner.hide();
    }
  }
};
</script>
