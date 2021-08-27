<template>
  <Header title="Fengshui Tracker" />
  <div class="container">
    <div class="grid">
      <div class="accordion">
        <div>
          <input type="checkbox" id="section1" class="accordion-input" />
          <label for="section1" class="accordion-label"
            >1. Insert a
            <span class="tooltip"
              >valid
              <span class="tooltiptext"
                >10 digits long and with the right prefix</span
              >
            </span>
            phone number</label
          >
          <div class="accordion-content">
            <NumberInput @onSubmit="onSubmit" :storedNumber="number" />
          </div>
        </div>

        <div>
          <input
            type="checkbox"
            id="section2"
            :disabled="!number"
            class="accordion-input"
          />
          <label for="section2" class="accordion-label"
            >2. Check if it's a fengshui number</label
          >
          <div class="accordion-content">
            <div class="button-container">
              <Button @check-fengshui="isInputFengshui" text="Check" />
            </div>
            <div v-if="showResult">
              <div v-if="isFengshui" class="result green">
                <img
                  :src="require('./assets/icons/check.png')"
                  :alt="operator"
                />
                {{ number }} is a fengshui number
              </div>
              <div v-else class="result red">
                <img
                  :src="require('./assets/icons/remove.png')"
                  :alt="operator"
                />
                <p>{{ number }} is not a fengshui number</p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <p class="middle">OR</p>
      <div>
        <div class="drag">
          <Drag
            @load="onFileRead"
            @get-fengshui-file="checkFile"
            @remove-file="clearData"
          />
        </div>
        <div v-if="fengShuiList.length > 0">
          <Table :fengShuiList="fengShuiList" />
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Header from "./components/Header.vue";
import NumberInput from "./components/NumberInput.vue";
import Drag from "./components/Drag.vue";
import Button from "./components/Button.vue";
import Table from "./components/Table.vue";
import json from "./assets/taboo.json";
import { tableData } from "../typings";
export default defineComponent({
  name: "App",
  components: {
    Header,
    NumberInput,
    Button,
    Drag,
    Table,
  },
  data() {
    return {
      number: "",
      tabooNum: [] as string[],
      isFengshui: false,
      showResult: false,
      step: 1,
      fengShuiList: [] as tableData[],
      fileData: [] as string[],
      operators: [
        { name: "viettel", prefixes: ["086", "096", "097"] },
        { name: "mobiphone", prefixes: ["089", "090", "093"] },
        { name: "vinaphone", prefixes: ["088", "091", "094"] },
      ],
    };
  },
  mounted() {
    this.tabooNum = json.taboo_numbers;
  },
  methods: {
    onSubmit(data: string) {
      this.number = data;
      this.showResult = false;
    },
    sumDigits(number: string, part: number) {
      const arr =
        part === 1 ? number.slice(0, 5).split("") : number.slice(-5).split("");
      return arr.reduce((acc, x) => (acc = acc += Number(x)), 0);
    },

    checkFengshui(num: string) {
      if ((this.tabooNum as string[]).includes(num.slice(-2))) {
        this.showResult = true;
        return false;
      }
      if (
        this.sumDigits(num, 1) === 24 &&
        [28, 29].includes(this.sumDigits(num, 2))
      ) {
        return true;
      } else return false;
    },
    isInputFengshui() {
      this.isFengshui = false;
      this.checkFengshui(this.number)
        ? (this.isFengshui = true)
        : (this.isFengshui = false);
      this.showResult = true;
    },
    onFileRead(data: any) {
      const arrayData = data.split("\r\n");
      this.fileData = arrayData;
    },
    checkFile() {
      this.fengShuiList = [];
      this.fileData.forEach((x: string) => {
        if (this.checkFengshui(x)) {
          const operator = this.getOperator(x);
          console.log(operator);
          this.fengShuiList.push({
            operator,
            num: x,
          });
        }
      });
    },
    clearData() {
      this.fileData = [];
      this.fengShuiList = [];
    },
    clearInput() {
      this.isFengshui = false;
      this.fengShuiList = [];
    },
    getOperator(num: string) {
      console.log("for each", num.slice(0, 3));
      return this.operators
        .map((op) => (op.prefixes.includes(num.slice(0, 3)) ? op.name : null))
        .filter((x) => x)[0];
    },
  },
});
</script>

<style>
body {
  margin: 0;
  padding: 0;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
.container {
  width: 100%;
  max-width: 1068px;
  margin: 0 auto;
}
.grid {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  padding: 2.5rem 0;
  margin: 0 1rem;
}

.btn {
  display: inline-block;
  background: black;
  color: #fff;
  width: 200px;
  border: 1px solid transparent;
  padding: 10px 20px;
  margin: 5px auto;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
  transition: all 0.2s ease-in;
}
.btn:hover {
  border: 1px solid;
}
.btn:active {
  transform: translateY(-3px);
}
.btn-disabled {
  opacity: 0.7;
}
.btn-disabled:hover {
  border: 1px solid transparent;
  cursor: not-allowed;
}

.button-container {
  display: grid;
  place-items: center;
}
.red {
  color: red;
}
.green {
  color: green;
}
.drag {
  width: 400px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  border-radius: 5px;
  overflow: hidden;
  background: #009578;
  padding: 14px 20px;
}
.accordion {
  width: 400px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  border-radius: 5px;
  /* overflow: hidden; */
  background: #009578;
}
.accordion-label,
.accordion-content {
  padding: 14px 20px;
}
.accordion-label {
  display: block;
  color: white;
  cursor: pointer;
  position: relative;
  transition: background 0.1s;
}
.accordion-label:hover {
  background: rgba(0, 0, 0, 0.1);
}
.accordion-label::after {
  content: "";
  position: absolute;
  top: 50%;
  right: 20px;
  transform: translateY(-50%);
  width: 12px;
  height: 6px;
  background-image: url('data:image/svg+xml;utf8,<svg width="100" height="50" xmlns="http://www.w3.org/2000/svg"><polygon points="0,0 100,0 50,50" style="fill:%23FFFFFF99;" /></svg>');
  background-size: contain;
  transition: transform 0.4s;
}
.accordion-content {
  background: white;
  line-height: 1.6;
  font-size: 0.85em;
  display: none;
}
.accordion-input {
  display: none;
}
.accordion-input:checked ~ .accordion-content {
  display: block;
}
.accordion-input:checked ~ .accordion-label::after {
  transform: translateY(-50%) rotate(0.5turn);
}
.result {
  display: flex;
  align-items: center;
  margin: 1rem 0;
  font-size: 1.2rem;
}
.result img {
  max-width: 50px;
  margin-right: 1rem;
}
.tooltip {
  position: relative;
  display: inline-block;
  border-bottom: 1px solid black;
}

.tooltip .tooltiptext {
  visibility: hidden;
  width: 120px;
  background-color: black;
  color: #fff;
  text-align: center;
  border-radius: 6px;
  padding: 5px;
  position: absolute;
  font-size: 0.8rem;
  z-index: 10;
  bottom: 150%;
  left: 50%;
  margin-left: -60px;
}

.tooltip .tooltiptext::after {
  content: "";
  position: absolute;
  top: 100%;
  left: 50%;
  margin-left: -5px;
  border-width: 5px;
  border-style: solid;
  border-color: black transparent transparent transparent;
}
.tooltip:hover .tooltiptext {
  visibility: visible;
}
.middle {
  padding: 1rem;
}
@media only screen and (max-width: 992px) {
  .container {
    padding: 0 1rem;
  }
  .grid {
    flex-direction: column;
    align-items: center;
  }
}
</style>
