<template>
  <h1 class="text-3xl font-bold mt-52">Form Validation</h1>
  <div class="w-screen">
    <div id="app" class="flex w-full h-full justify-center items-center">
      <form class="w-full" @submit.prevent="submitForm" autocomplete="off">
        <!--      NAME INPUT           -->

        <div class="form-group">
          <label for="name" class="mt-4 block">Name:</label>
          <input
            @blur="v$.form.name.$touch()"
            v-model="form.name"
            id="name"
            class="mx-auto appearance-none rounded-none relative block w-2/3 px-3 py-2 border placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:z-10 sm:text-sm"
            :class="{ 'border-red-300': v$.form.name.$error, 'border-green-300': !v$.form.name.$invalid }"
          />
          <p
            v-if="v$.form.name.$error"
            class="error-message text-red-400 bold mt-2 font-bold"
          >The name field is required</p>
        </div>

        <!--      AGE INPUT           -->

        <div class="form-group">
          <label for="age" class="mt-4 block">Age:</label>
          <input
            @blur="v$.form.age.$touch()"
            v-model="form.age"
            id="age"
            class="w-2/3 mx-auto appearance-none rounded-none relative block px-3 py-2 border placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:z-10 sm:text-sm"
            :class="{ 'border-red-300': v$.form.age.$error, 'border-green-300': !v$.form.age.$invalid }"
          />

          <div v-show="v$.form.age.$error" class="AgesErrorContainer">
            <p
              v-if="v$.form.age.required.$invalid"
              class="error-message text-red-400 font-bold mt-2"
            >The age field is required</p>
            <p
              v-else-if="v$.form.age.integer.$invalid"
              class="error-message text-red-400 font-bold mt-2"
            >The age field should be an integer</p>
            <p
              v-else-if="v$.form.age.between.$invalid"
              class="error-message text-red-400 font-bold mt-2"
            >You should be at least 12 and younger than 100 to continue</p>
          </div>
        </div>

        <!--      EMAIL INPUT           -->

        <div class="form-group">
          <label for="email" class="mt-4 block">Email:</label>
          <input
            @blur="v$.form.email.$touch()"
            v-model="form.email"
            id="email"
            class="mx-auto appearance-none rounded-none relative block w-2/3 px-3 py-2 border placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:z-10 sm:text-sm"
            :class="{ 'border-red-300': v$.form.email.$error, 'border-green-300': !v$.form.email.$invalid && form.email }"
          />

          <p
            v-if="v$.form.email.requiredIf.$params.prop && !form.email"
            class="error-message text-red-400 bold mt-2 font-bold"
          >Email is required so we can send you the newsletter</p>
          <p
            v-else-if="v$.form.email.$error"
            class="error-message text-red-400 bold mt-2 font-bold"
          >Enter a valid Email</p>
        </div>

        <!--      GITHUB INPUT          -->

        <div class="form-group github-username">
          <label class="mt-4 block" for="github-username">GitHub username:</label>
          <input
          class="mx-auto appearance-none rounded-none relative block w-2/3 px-3 py-2 border placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:z-10 sm:text-sm"
            v-model.lazy="v$.form.githubUsername.$model"
            :class="{ 'border-red-300':v$.form.githubUsername.$error, 'border-green-300':!v$.form.githubUsername.$invalid && v$.form.githubUsername.$model && v$.form.githubUsername.$dirty }"
            id="github-username"
          />
          <span v-show="v$.form.githubUsername.$pending" class="fa fa-spinner fa-spin"></span>
          <p
            v-if="!v$.form.githubUsername.exists && v$.form.githubUsername.$error"
            class="error-message"
          >There is not GitHub user with this username</p>
        </div>

        <!--      FOOD INPUT          -->

        <div class="form-group">
          <label class="mt-4 block" for="food">Pizza Or Burger:</label>
          <input
            v-model.number="form.food"
            class="mx-auto appearance-none rounded-none relative block w-2/3 px-3 py-2 border placeholder-gray-500 text-gray-900 rounded-t-md focus:outline-none focus:z-10 sm:text-sm"
            @blur="v$.form.food.$touch()"
            :class="{ 'border-red-300': v$.form.food.$error, 'border-green-300': !v$.form.food.$invalid && form.food }"
          />
          <p
            v-if="v$.form.food.$error && v$.form.food.pizzaOrBurger"
            class="error-message text-red-400 font-bold mt-2"
          >Sorry! We only serve pizzas and burgers</p>
        </div>

        <!--      SUBMIT BUTTON          -->

        <div class="form-group checkbox w-2/3 mx-auto text-left">
          <input
            v-model="form.newsletter"
            type="checkbox"
            id="newsletter"
            class="mr-4 mt-4 scale-150"
          />
          <label for="newsletter">Subscribe to the newsletter:</label>
        </div>

        <!--      SUBMIT BUTTON          -->

        <div>
          <button
            class="w-1/3 mx-auto mt-12 py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-emerald-400 hover:bg-emerald-500 focus:outline-none cursor-pointer"
          >Submit</button>
        </div>
      </form>
    </div>
  </div>
</template>

<!--  
    on peux metre @blur='' pour metre l'event lorsque l'on quitte l'input, ou
    metre v-model="v$.form.name.$model" qui change la valeur de $dirty( a été touché ) et la data dans form.name
-->

<script>
import useVuelidate from '@vuelidate/core'
import { required, integer, between, email, requiredIf, helpers } from '@vuelidate/validators'
import axios from 'axios'

export default {
  setup(){
    return {v$: useVuelidate()}
  },
  data() {
    return {
      form: {
        name: '',
        age: '',
        email: '',
        food: '',
        newsletter: null,
        githubUsername: null
      }
    }
  },
  validations() {
    return {
      form: {
        name: { required },
        age: { required, integer, between: between(12, 100) },
        email: { email, requiredIf: requiredIf(this.form.newsletter), },
        food: {
          pizzaOrBurger: value => {  // custom validator
            // doit renvoyer un boolean
            return value.toLowerCase() === 'pizza' || value.toLowerCase() === 'burger' || !helpers.req(value) // le !helpers.req(value) rend le champ optionnel
          }
        },
        githubUsername: {
          exists (value) {
            if (!helpers.req(value)) {
              return true
            }
            let resp;
            axios.get(`//api.github.com/users/${value}`).then(e=> resp =true).catch(e=> resp = false)
            return resp
          }
        },

      }
    }
  },
  methods: {
    submitForm() {
      this.v$.form.name.$touch() // pad d'erreur si on ne fait pas cette fonction et que on submit sans toucher a rien puisque l'erreur s'affiche seulement si $dirty = true, donc que l'input a été touché
      this.v$.form.age.$touch()
      this.v$.form.email.$touch()
      this.v$.form.food.$touch()
      // this.v$.$touch()  met tout les input en $dirty = true
      // this.v$.form.$touch()  met tout les input dans form en $dirty = true
      if (!this.v$.form.$invalid) {
        console.log('📝 Form Submitted', this.form)
      } else {
        console.log('❌ Invalid form')
      }
    }
  },


}

</script>
