<template>
  <!--
    Add the directive called contactBubble to
    this element
  -->
  <div id="app">
    <div class="section">
      <button id="btnAddContact" @click.prevent="openNew">Add Contact</button>
    </div>
    <div class="section">
      <!-- Two way bind query to this input field -->
      <input id="search" placeholder="search" type="text">
    </div>
    <app-contactform v-bind="formSettings" v-bind:contact="contact"></app-contactform>
    <!--
      Edit app-contacts component to receive
      contacts property through search results
    -->
    <app-contacts></app-contacts>
  </div>
</template>

<script>
import Contacts from './components/Contacts'
import ContactForm from './components/ContactForm'
import { ContactBus } from './bus/ContactBus'

export default {
  name: 'App',
  components: {
    'app-contacts': Contacts,
    'app-contactform': ContactForm
  },
  data () {
    /**
     * Create the necessary data variables here
     */
    return {
      contacts: '',
      contact: {
        firstname: '',
        lastname: '',
        email: [''],
        phoneNumber: ['']
      },
      formSettings: {
        /**
         * Create members boolean variables visible and newCon
         * with default values false. Create member variable
         * index with default value ''
         */
      }
    }
  },
  computed: {
    search: {
      /**
       * Complete this function to
       * search through contacts
       * by first and last name, email & phone number
       * and return an array with the results
       */
      get: function () {
        return ''
      }
    }
  },
  directives: {
    contactBubble: {
      /**
       * Complete this directive
       * to make its elements
       * background-color: rgb(159, 159, 199);
       * border-radius: 20px;
       * color: white;
       * font-variant: all-petite-caps;
       */
    }
  },
  methods: {
    sortContacts: function () {
      /**
       * Complete this function to return contacts
       * sorted by lastname in ascending order
       */
      return ''
    },
    openNew: function () {
      /**
       * This function should be used to show new contact
       * form on screen.
       */
    },
    close: function (data) {
      this.formSettings.visible = !data
    }
  },
  beforeCreate () {
    /**
     * Each instance of App will load a separate clone of the mocks contacts data
     */
    const mockContacts = require('./assets/contacts.json')
    const clone = JSON.parse(JSON.stringify(mockContacts))
    this.contacts = clone
  },
  created: function () {
    ContactBus.$on('addCon', (data) => {
      /**
       * Finish this method so that data will
       * be appended to contacts if formSettings.newCon
       * is true and reset formSettings to default values
       */
    })

    ContactBus.$on('noAddCon', (data) => {
      this.nullContact = data
    })

    ContactBus.$on('close', (data) => {
      this.close(data)
    })

    ContactBus.$on('delCon', (index) => {
      /**
       * This method should remove index from contacts
       * and set formSettings to their default values
       */
    })

    ContactBus.$on('editCon', (contactIndex) => {
      /**
       * Finish this method to show the form
       * with the index of the contact to be
       * displayed.
       */
      this.formSettings.newCon = false
      this.formSettings.visible = true
    })
  }
}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.section {
  margin: 30px;
}
</style>
