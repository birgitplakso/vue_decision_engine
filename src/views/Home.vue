<template>
  <div class="home">
    <h1>Welcome to Loan Decision Engine</h1>
    <br/>
    <h3>Request for Loan </h3>
    <br/>

    <table style="width:100%" align="center">
      <tr>
        <th>Amount from 2000 EUR to 10000 EUR:</th>
      </tr>
      <tr>
        <td><input v-model="requestedAmount" type="number" min="0" step="100" placeholder="Enter loan amount "></td>
      </tr>
      <tr>
        <th>Loan period can be from 12 to 60 months</th>
      </tr>
      <tr>
        <td><input v-model="requestedPeriod" type="number" min="0" step="1" placeholder="Enter period in months "></td>
      </tr>
      <tr>
        <th>Choose customer code:</th>
      </tr>
      <tr>
        <td>
          <b-form-select v-model="selected" :options="options"></b-form-select>
        </td>
      </tr>
    </table>
    <br/>

    <div>
      <b-button variant="success" v-on:click="requestLoan()">Send request</b-button>
    </div>
    <br/>
    <div id="error">
      <p v-if="errorMessage" class="regular-text" >{{ errorMessage }}</p>
    </div>

    <p v-if="loanResponse" class="regular-text" >
      Requested loan is {{ loanResponse.enteredAmount }} EUR for {{ loanResponse.enteredPeriod }}
      month period. <br/>
    <p v-if="loanResponse && loanResponse.proposedAmount==0"> The maximum possible loan for the entered
    period is below minimum (2000EUR). </p>
    <p v-if="loanResponse && loanResponse.proposedAmount!=0"> The maximum amount for requested period
      is {{ loanResponse.proposedAmount }} EUR. </p>
    <p v-if="loanResponse && loanResponse.proposedPeriod==0"> Calculated loan period for the entered
      amount exceeds the maximum allowed loan period (60 months). </p>
    <p v-if="loanResponse && loanResponse.proposedPeriod!=0"> We could provide the requested loan for
      {{ loanResponse.proposedPeriod }} month period. </p>



  </div>
</template>

<script>
export default {
  data() {
    return {
      selected: null,
      options: [
        {value: null, text: 'Customer codes by segment:'},
        {value: '49002010965', text: '49002010965'},
        {value: '49002010976', text: '49002010976'},
        {value: '49002010987', text: '49002010987'},
        {value: '49002010998', text: '49002010998'}
      ],
      'requestedAmount': '',
      'requestedPeriod': '',
      'loanResponse': '',
      'errorMessage': '',

    }
  },
  methods: {
    'requestLoan': function () {
      if (this.requestedPeriod < 12 || this.requestedPeriod > 60) {
        this.errorMessage = 'Loan period is out of bounds';
        return;
      } else if (this.requestedAmount < 2000 || this.requestedAmount > 10000) {
        this.errorMessage = 'Loan amount is out of bounds';
        return;
      } else if(this.selected===null){
        this.errorMessage = 'Enter customer code';
        return;
      }
      this.$http.get('api/loanDecision/' + this.requestedAmount
          + '/' + this.requestedPeriod + '/' + this.selected)
          .then(response => {
            console.log(response);
            this.loanResponse = response.data
          })
          .catch(response =>
                  this.errorMessage = 'Error! Customer has dept, can not process loan request!'
              // alert('juhtus viga')
          )
    }
  }
}
</script>

<style>


#error{
  font-palette: light;
  color: crimson;
}

</style>
