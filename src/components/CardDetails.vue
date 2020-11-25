<template>
  <div class="flexbox-container">
    <b-card class="card-summary w-25">
      <h3>{{ formattedCardNumber }}</h3>
      <div class="d-flex justify-content-between">
        <div>
          <label class="mb-0">Card Holder</label>
          <h5>{{ formattedCardHolderName }}</h5>
        </div>
        <div>
          <label class="mb-0">Expires</label>
          <h5>{{ formattedExpires }}</h5>
        </div>
      </div>
    </b-card>
    <b-card class="w-50 pt-5">
      <div>
        <div role="group">
          <label class="mb-0 mt-3">Card Number</label>
          <b-form-input v-model="cardNumber" @keypress="validateCardNumber" />
        </div>
        <div role="group">
          <label class="mb-0 mt-3">Card Holder Name</label>
          <b-form-input v-model="cardHolderName" />
        </div>
        <div class="row">
          <div role="group" class="col-8">
            <label class="mb-0 mt-3">Expires</label>
            <div class="d-flex justify-content-between">
              <b-form-select
                class="mr-3"
                v-model="cardMonth"
                :options="months"
              />
              <b-form-select class="ml-3" v-model="cardYear" :options="years" />
            </div>
          </div>
          <div role="group" class="col-4">
            <label class="mb-0 mt-3">CVV</label>
            <b-form-input
              type="password"
              v-model="cardCVV"
              @keypress="validateCVV"
            />
          </div>
        </div>
      </div>
      <b-button class="submit-btn" variant="warning">Submit</b-button>
    </b-card>
  </div>
</template>

<script>
export default {
  name: "CardDetails",

  data() {
    return {
      cardNumber: "",
      cardHolderName: "",
      cardMonth: null,
      cardYear: null,
      cardCVV: "",
      months: [{ value: null, text: "MM", disabled: true }],
      years: [{ value: null, text: "YY", disabled: true }],
    };
  },

  computed: {
    formattedCardNumber() {
      const defaultValue = ["XXXX", "XXXX", "XXXX", "XXXX"];
      for (let i = 0; i < 16; i += 4) {
        let cardSubstring = this.cardNumber.substr(i, 4);
        if (cardSubstring.length < 4) {
          cardSubstring += "XXXX";
        }
        defaultValue[i / 4] = cardSubstring.substr(0, 4);
      }
      return defaultValue.join(" ");
    },
    formattedCardHolderName() {
      const defaultValue = ["XXXXX", "XXXXX"];
      const names = this.cardHolderName.split(" ").filter(Boolean);
      for (let i = 0; i < names.length; i++) {
        if (i < 2) {
          defaultValue[i] = names[i];
        } else {
          defaultValue.push(names[i]);
        }
      }
      return defaultValue.join(" ");
    },
    formattedExpires() {
      const defaultValue = ["XX", "XX"];
      if (this.cardMonth) {
        defaultValue[0] =
          this.cardMonth < 10 ? "0" + this.cardMonth : this.cardMonth;
      }
      if (this.cardYear) {
        defaultValue[1] = `${this.cardYear}`.substr(2, 2);
      }
      return defaultValue.join("/");
    },
  },

  mounted() {
    this.setMonthYears();
  },

  methods: {
    setMonthYears() {
      for (let i = 1; i <= 12; i++) {
        this.months.push(i);
      }
      const currentYear = new Date().getFullYear();
      for (let i = currentYear; i <= currentYear + 20; i++) {
        this.years.push(i);
      }
    },
    validateCardNumber($event) {
      let keyCode = $event.keyCode ? $event.keyCode : $event.which;
      if (keyCode < 48 || keyCode > 57 || this.cardNumber.length > 15) {
        $event.preventDefault();
        return;
      }
    },
    validateCVV($event) {
      let keyCode = $event.keyCode ? $event.keyCode : $event.which;
      if (keyCode < 48 || keyCode > 57 || this.cardCVV.length > 2) {
        $event.preventDefault();
        return;
      }
    },
  },
};
</script>

<style scoped>
.flexbox-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
.card-summary {
  background-color: wheat;
  top: 70px;
  z-index: 1;
}
.submit-btn {
  width: 100%;
  margin-top: 20px;
}
</style>