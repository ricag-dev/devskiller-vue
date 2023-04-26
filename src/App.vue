<template>
  <!--
    Add the directive called contactBubble to
    this element
  -->
  <div id="app" v-contact-bubble>
    <div class="section">
      <button id="btnAddContact" @click.prevent="openNew">Add Contact</button>
    </div>
    <div class="section">
      <!-- Two way bind query to this input field -->
      <input id="search" placeholder="search" type="text" v-model="query">
    </div>
    <app-contactform v-bind="formSettings" v-bind:contact="contact"></app-contactform>
    <!--
      Edit app-contacts component to receive
      contacts property through search results
    -->
    <app-contacts :contacts="search"></app-contacts>
  </div>
</template>

<script>
import Contacts from './components/Contacts'
import ContactForm from './components/ContactForm'
import { ContactBus } from './bus/ContactBus'
import data from './assets/contacts.json'

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
      contacts: this.sortContacts(),
      contact: {
        firstname: '',
        lastname: '',
        email: [''],
        phoneNumber: ['']
      },
      query: '',
      formSettings: {
        /**
         * Create members boolean variables visible and newCon
         * with default values false. Create member variable
         * index with default value ''
         */
        index: '',
        newCon: false,
        visible: false
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
        return this.contacts.filter((contact) => {
          const params = [
            contact.firstname.toLowerCase(),
            contact.lastname.toLowerCase(),
            contact.email.join(' ').toLowerCase(),
            contact.phoneNumber.join(' ').toLowerCase()
          ].join(' ')
          const finalQuery = this.query.trim().toLowerCase()

          if (!finalQuery) {
            return true
          } else {
            return params.indexOf(finalQuery) > -1
          }
        })
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
      bind (el) {
        el.style.background = 'rgb(159, 159, 199)'
        el.style.borderRadius = '20px'
        el.style.color = 'white'
        el.style.fontSize = '20px'
        el.style.fontVariant = 'all-petite-caps'
      }
    }
  },
  methods: {
    sortContacts: function () {
      /**
       * Complete this function to return contacts
       * sorted by lastname in ascending order
       */
      return data.sort((a, b) => {
        const nameA = a.lastname.toLowerCase()
        const nameB = b.lastname.toLowerCase()
        if (nameA < nameB) return -1
        if (nameA > nameB) return 1
        return 0
      })
    },
    openNew: function () {
      /**
       * This function should be used to show new contact
       * form on screen.
       */
      this.contact = {
        id: this.contacts.length,
        firstname: '',
        lastname: '',
        email: [''],
        phoneNumber: ['']
      }

      this.formSettings.newCon = true
      this.formSettings.visible = true
    },
    close: function (data) {
      this.formSettings.index = null
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
      this.contacts.push(data)
    })

    ContactBus.$on('noAddCon', (data) => {
      this.nullContact = data
    })

    ContactBus.$on('close', (data) => {
      this.close(data)
    })

    ContactBus.$on('delCon', (index) => {
      if (!isNaN(parseInt(index))) {
        this.contacts.splice(index, 1)
      }
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
      this.contact = { ...this.contacts[contactIndex] }
      this.formSettings.index = contactIndex
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
