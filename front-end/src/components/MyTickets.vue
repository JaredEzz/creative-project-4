<template>
  <div class="main">
    <h1>My Appointments</h1>
    <div>
      <button @click="setCreating" class="pure-button button-xsmall">
        <i class="fas fa-plus" />
      </button>
    </div>
    <form class="pure-form" v-if="creating" @submit.prevent="addTicket">
      <br>
      <legend>What do you need our services for?</legend>
      <fieldset>
        <textarea placeholder="Describe your event or desired result." v-model="problem"></textarea>
        <br />
        <br />
        <button @click="cancelCreating" class="pure-button space-right">Cancel</button>
        <button class="pure-button pure-button-primary" type="submit">Submit</button>
      </fieldset>
    </form>
    <div v-for="ticket in tickets" v-bind:key="ticket.id">
      <div class="ticket">
        <div class="problem">
          <h3><label>{{ticket.status}}</label> Project Submitted at {{time(ticket.created)}}, ready to be Scheduled</h3>
          <p>{{ticket.problem}}</p>
          <p v-if="ticket.response"><i>{{ticket.response}}</i></p>
          <p v-else><i>Thank you for submitting, we will get back to you soon.</i></p>
        </div>
      </div>
    </div>
    <h2>Logged in as: {{user.firstName}} {{user.lastName}} <button @click="logout" class="pure-button pure-button-primary">Logout</button></h2>

  </div>

</template>

<script>
  import axios from 'axios';
  import moment from 'moment';
  export default {
    name: 'MyTickets',
    data() {
      return {
        creating: false,
        problem: '',
        tickets: [],
      }
    },
    computed: {
      user() {
        return this.$root.$data.user;
      }
    },
    created() {
      this.getTickets();
    },
    methods: {
      async logout() {
        try {
          await axios.delete("/api/users");
          this.$root.$data.user = null;
        } catch (error) {
          this.$root.$data.user = null;
        }
      },
      async getTickets() {
        try {
          let response = await axios.get("/api/tickets");
          this.tickets = response.data.tickets;
          return true;
        } catch (error) {
          return null;
        }
      },
      time(d) {
        return moment(d).format('D MMMM YYYY, h:mm:ss a');
      },
      setCreating() {
        this.creating = true;
      },
      cancelCreating() {
        this.creating = false;
      },
      async addTicket() {
        try {
          await axios.post("/api/tickets", {
            problem: this.problem
          });
          this.problem = "";
          this.creating = false;
          this.getTickets();
          return true;
        } catch (error) {
          return false;
        }
      }
    }
  };
</script>

<style scoped>
  textarea {
    width: 100%;
    max-width: 500px;
  }

  h3 {
    font-size: 12px;
    font-weight: normal;
    background-color: #6bbcff;
    padding: 10px 20px;
  }


  label {
    background-color: #090167;
    color: white;
    padding: 5px;
    border-radius: 30px;
    font-size: 12px;
    margin-right: 10px;
  }

  .pure-button{
    background-color: #277E8E;
    color: white;
    font-size: 14px;
    font-weight: bold;
  }

  .ticket {}
</style>

