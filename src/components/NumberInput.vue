<template>
  <form @submit.prevent="submitForm">
    <div class="form-control">
      <label for="number">Phone number</label>
      <p class="errors" v-if="!number.isValid">{{ errorMessage }}</p>
      <input
        id="number"
        @keypress="isValidInput($event)"
        v-model="number.val"
        @blur="clearValidity('number')"
      />
    </div>
  </form>
  <div v-if="inputOperator" class="result">
    <img :src="getImgUrl(inputOperator)" v-bind:alt="operator" />
    <p>{{ storedNumber }}</p>
  </div>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  props: {
    storedNumber: String,
  },
  data() {
    return {
      number: { val: "", isValid: true },
      formIsValid: true,
      errorMessage: "",
      inputOperator: null,
      isFensghui: null,
      showResult: false,
      operators: [
        { name: "viettel", prefixes: ["086", "096", "097"] },
        { name: "mobiphone", prefixes: ["089", "090", "093"] },
        { name: "vinaphone", prefixes: ["088", "091", "094"] },
      ],
    };
  },

  methods: {
    getImgUrl(operator) {
      return require("../assets/media/" + operator + ".svg");
    },
    isInvalid(message) {
      this.formIsValid = false;
      this.number.isValid = false;
      this.errorMessage = message;
      this.inputOperator = null;
    },
    isValidInput(e) {
      let char = String.fromCharCode(e.keyCode);
      if (/^[0-9]+$/.test(char) || e.keyCode === 13) {
        this.errorMessage = "";
        return true;
      } else {
        this.isInvalid("Only digits are allowed");
        e.preventDefault();
      }
    },
    clearValidity(input) {
      this[input].isValid = true;
    },
    validateForm() {
      this.formIsValid = true;
      if (this.number.val.length !== 10) {
        this.isInvalid("Phone number must be 10 digits");
        return;
      }
      this.operators.forEach((op) => {
        if (op.prefixes.includes(this.number.val.slice(0, 3)))
          this.inputOperator = op.name;
      });
      if (!this.inputOperator)
        this.isInvalid("This number is not mobiphone, vinaphone or viettel");
    },
    submitForm() {
      this.validateForm();
      if (!this.formIsValid) return;
      this.$emit("onSubmit", this.number.val);
      this.number.val = "";
    },
  },
});
</script>

<style scoped>
form {
  margin: 1rem;
  border: 1px solid #ccc;
  border-radius: 12px;
  padding: 1rem;
  width: 300px;
}

.form-control {
  margin: 0.5rem 0;
}

label {
  font-weight: bold;
  margin-bottom: 0.5rem;
  display: block;
}

input {
  display: block;
  width: 100%;
  font: inherit;
  border: 1px solid #ccc;
  padding: 0.15rem;
}

input:focus,
textarea:focus {
  border-color: #3d008d;
  background-color: #faf6ff;
  outline: none;
}

.errors {
  font-weight: bold;
  color: red;
}

img {
  max-width: 120px;
  height: 30px;
  width: 100%;
}
.result {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-weight: 700;
  font-size: 1.2rem;
  padding: 0 1rem;
}
</style>
