<template>
  <f7-page>
    <f7-navbar title="Welcome to the Fitness Cloud!" back-link="Back"></f7-navbar>

    <f7-block>
      <center><img src="static/aws-logo.svg" width=50% height=50%></center>
    </f7-block>

    <f7-block>
      <p>We just need a few more details to get you started on a journey of healthy lifestyle.</p>
    </f7-block>

    <f7-list inline-labels no-hairlines-md>
      
      <f7-list-item>
        <f7-icon icon="demo-list-icon" slot="media"></f7-icon>
        <f7-label>Height</f7-label>
        <f7-input @input="data.input.height = $event.target.value" type="text" placeholder="Your height in cm" clear-button></f7-input>
      </f7-list-item>

      <f7-list-item>
        <f7-icon icon="demo-list-icon" slot="media"></f7-icon>
        <f7-label>Weight</f7-label>
        <f7-input @input="data.input.weight = $event.target.value" type="text" placeholder="Your weight in kg" clear-button></f7-input>
      </f7-list-item>

      <f7-list-item>
        <f7-icon icon="demo-list-icon" slot="media"></f7-icon>
        <f7-label>Calories</f7-label>
        <f7-input @input="data.input.caloriesTargetPerDay = $event.target.value" type="text" placeholder="Per day target" clear-button></f7-input>
      </f7-list-item>

  </f7-list>

  <f7-block>
    <f7-row center>
        <f7-col width="50" center>
          <f7-button @click="createUserDetails()" center>Submit</f7-button>
        </f7-col>
    </f7-row>
  </f7-block>

  </f7-page>
</template>

<script>
import { Auth, API, graphqlOperation } from 'aws-amplify'
import { createUser } from '../graphql/mutations'

export default {
  data() {
    return {
      data: {
        input: {
          height: 0,
          weight: 0,
          caloriesTargetPerDay: 0,
          caloriesConsumed: 0,
          username: '',
          id: ''
        }
      }
    }
  },
  beforeCreate() {
    // TODO: Fix this to use localStorage 
    Auth.currentAuthenticatedUser()
    .then(user => {
      this.data.input.username = user.username
      this.data.input.id = user.attributes.sub
    })
    .catch(err => console.log(err));
  },
  methods: {
    async createUserDetails() {
      try {
        this.appSyncLogger.info('Invoking the createUser Mutation')

        const { data: { createUser: { items }} } = await API.graphql(graphqlOperation(createUser, this.data))

        this.appSyncLogger.info('createUser invoked successfully')
      } catch (err) {
        this.appSyncLogger.error('Error while invoking createUser: ' + JSON.stringify(err))
      }

      // Ready to go! Navigate to home screen
      this.$f7router.navigate('/home/')
    }
  }
}

</script>