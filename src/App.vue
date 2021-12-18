<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <div>Flutter is Better</div>
  <h6>Yaroslav©</h6>

  <div id="two-way-binding">
    <p>{{ answer }}</p>
    <input ref="cInp" type="text" v-model="calcInput"/>
    <span v-for="operation in (OperationsConstants)" v-bind:key="operation">
      <ButtonComponent @send-message="processInput" :function-data="operation"></ButtonComponent>
    </span>
  </div>
</template>

<script>
import ButtonComponent from './components/button_component'
import {OperationsConstants} from "./constants/operations_constants.ts";

function calculate(input) {

  const f = {
    add: '+',
    sub: '-',
    div: '/',
    mlt: '*',
    mod: '%',
    exp: '^'
  };

  // Create array for Order of Operation and precedence
  f.ooo = [
    [
      [f.mlt],
      [f.div],
      [f.mod],
      [f.exp]
    ],
    [
      [f.add],
      [f.sub]
    ]
  ];

  input = input.replace(/[^0-9%^*()\-+.]/g, ''); // clean up unnecessary characters

  var output;
  for (var i = 0, n = f.ooo.length; i < n; i++) {

    // Regular Expression to look for operators between floating numbers or integers
    var re = new RegExp('(\\d+\\.?\\d*)([\\' + f.ooo[i].join('\\') + '])(\\d+\\.?\\d*)');
    re.lastIndex = 0; // take precautions and reset re starting pos

    // Loop while there is still calculation for level of precedence
    while (re.test(input)) {
      output = _calculate(RegExp.$1, RegExp.$2, RegExp.$3);
      if (isNaN(output) || !isFinite(output))
        return output; // exit early if not a number
      input = input.replace(re, output);
    }
  }

  return output;

  function _calculate(a, op, b) {
    a = a * 1;
    b = b * 1;
    switch (op) {
      case f.add:
        return a + b;
      case f.sub:
        return a - b;

      case f.div:
        return a / b;

      case f.mlt:
        return a * b;

      case f.mod:
        return a % b;

      case f.exp:
        return Math.pow(a, b);

      default:
        null;
    }
  }
}

export default {
  name: 'App',
  components: {
    ButtonComponent
  },
  data() {
    return {
      calcInput: '',
      OperationsConstants
    }
  },
  watch: {
    calcInput: function (newValue) {
      this.processString(newValue);
    }
  },
  methods: {
    processString(input) {
      this.answer = calculate(input);
    },
    processInput(operation) {
      if (operation === OperationsConstants.CLEAR_KEYWORD) {
        this.calcInput = '';
        this.answer = '';
      } else {
        this.calcInput += operation;
        this.$refs.cInp.focus();
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
