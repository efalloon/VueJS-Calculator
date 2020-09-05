<template>
  <div id="app">
    <div class="centered">
      <div class="enterField">
        <label id="errorLabel" class="errorLabel">Error</label>
        <label id="outputCase" class="outputLabel">00</label>
      </div>

      <!-- <div v-on:click="simCommand($event)" id="resetCalcultor" class="resetCalcultor"><p>CE</p></div> -->

      <div id="calculatorOptionsBar" class="scrollmenu">
        <div v-on:click="select($event)" id="rule-sine">sin</div>
        <div v-on:click="select($event)" id="rule-cosine">cos</div>
        <div v-on:click="select($event)" id="rule-tangent">tan</div>
        <div v-on:click="select($event)" id="rule-left-bracket">(</div>
        <div v-on:click="select($event)" id="rule-right-bracket">)</div>
        <!-- <div v-on:click="select($event)" id="rule-squared">𝑥²</div>
        <div v-on:click="select($event)" id="rule-times-ten">x10<sup>𝑥</sup></div> -->
      </div>

      <div id="commands-grid" class="grid-container-commands">
        <div v-on:click="select($event)" id="grid-add" class="grid-item-command"><p>+</p></div>
        <div v-on:click="select($event)" id="grid-subtract" class="grid-item-command"><p>-</p></div>
        <div v-on:click="select($event)" id="grid-divide" class="grid-item-command"><p>÷</p></div>
        <div v-on:click="select($event)" id="grid-multiply" class="grid-item-command"><p>x</p></div>
        <div v-on:click="select($event)" id="grid-percentage" class="grid-item-command"><p>%</p></div>
         <div v-on:click="select($event)" id="grid-plus-minus" class="grid-item-command"><p>±</p></div>
        <!-- <div v-on:click="select($event)" id="grid-backspace" class="grid-item-command"><p id="delHide" style="color: #999;">DEL</p></div> -->
      </div>

      <!-- <div v-on:click="simCommand($event)" id="delete" class="long-rounded-button-delete">Delete</div> -->
      <div v-on:click="select($event)" id="resetCalcultor" class="long-rounded-button-clear">Clear</div>
      <div v-on:click="select($event)" id="history" class="long-rounded-button-history">History</div>

      <div id="test" class="grid-container">
        <div v-on:click="select($event)" id="grid-value-1" class="grid-item">1</div>
        <div v-on:click="select($event)" id="grid-value-2" class="grid-item">2</div>
        <div v-on:click="select($event)" id="grid-value-3" class="grid-item">3</div>
        <div v-on:click="select($event)" id="grid-value-4" class="grid-item">4</div>
        <div v-on:click="select($event)" id="grid-value-5" class="grid-item">5</div>
        <div v-on:click="select($event)" id="grid-value-6" class="grid-item">6</div>
        <div v-on:click="select($event)" id="grid-value-7" class="grid-item">7</div>
        <div v-on:click="select($event)" id="grid-value-8" class="grid-item">8</div>
        <div v-on:click="select($event)" id="grid-value-9" class="grid-item">9</div>
        <div v-on:click="select($event)" id="grid-value-dot" class="grid-item">.</div>
        <div v-on:click="select($event)" id="grid-value-0" class="grid-item">0</div>
        <div v-on:click="select($event)" id="grid-value-equal" class="grid-item-equal">=</div>
      </div>

      <div id="history-backdrop" class="history-backdrop">
        <div class="space-bar"></div>
        <label class="history-label">History Data</label>
        <div v-on:click="select($event)" id="exit-history" class="exit-history">×</div>
        <div v-on:click="select($event)" id="clear-history" class="clear-history">Delete All</div>
        <table id="table-data-history" class="table-data-history"></table>
        <label id="table-history-data-empty" class="table-history-data-empty">There is no available stored data.</label>
      </div> 
    </div>
  </div>
</template>

<script>
// import json from "@/data.json";
// import Calculator from './components/Calculator.vue'

var currentEquationHTML = "00"
var equationNormal = ""
var maxFontSizeOutput = 50
var recordErrorMax = false

String.prototype.replaceAt = function(index, replacement) {
    return this.substr(0, index) + replacement + this.substr(index + replacement.length);
}

// Algorithm sets the appropriate style or warnings.
function globalAdjustment() {
  for (var idCount = 0; idCount < currentEquationHTML.length; idCount++) {
    let labelVar = document.getElementById('outputCase');
    let text = currentEquationHTML.charAt(idCount);
    let labelWidth = labelVar.offsetWidth;
    adjustSizing(labelVar, labelWidth, text)
  }
}
function adjustSizing(labelVar, labelWidth, text) {
  if (labelWidth >= 350) {
    let str = window.getComputedStyle(labelVar, null).getPropertyValue('font-size');
    let int = parseInt(str)
    if (int <= 30) {
      currentEquationHTML = currentEquationHTML.slice(0, -1);
      currentEquationHTML += "<br/>"; currentEquationHTML += text
      labelVar.innerHTML = currentEquationHTML;
      let labelHeight = labelVar.offsetHeight;
      if (labelHeight > 204) { 
        currentEquationHTML = currentEquationHTML.slice(0, -1);
        labelVar.innerHTML = currentEquationHTML;
        let errorLabelBounds = document.getElementById("errorLabel")
        errorLabelBounds.style.backgroundColor = "#007bff";
        errorLabelBounds.innerHTML = "Too Many Characters"
        recordErrorMax = true;
      }
    } else {
      maxFontSizeOutput -= 3
      let sizeHandle = maxFontSizeOutput.toString(); sizeHandle += "px";
      labelVar.style.fontSize = sizeHandle;
    }
  } 
}

var jsonData = [];
function equalCommand() {
  // alert(localStorage.length)
  // alert(Object.entries(localStorage))
  localStorage.removeItem("loglevel:webpack-dev-server")
  if (localStorage.getItem("loglevel:webpack-dev-server") === null) {
    console.log("loglevel:webpack-dev-server not detected. :)")
  } else {
    localStorage.removeItem("loglevel:webpack-dev-server")
  }

  let labelVar = document.getElementById('outputCase');
  let errorLabelBounds = document.getElementById("errorLabel")
  errorLabelBounds.style.backgroundColor = "red";
  errorLabelBounds.innerHTML = "Error Occured in Calculation"
  maxFontSizeOutput = 50
  recordErrorMax = false
  var answer = eval(equationNormal);//"(Math.pow(2,2)*5)+2")
  
  jsonData = []
  if (localStorage.length > 0) {
    for (var iGET = 0; iGET < JSON.parse(window.localStorage.getItem('db-trial')).length; iGET++) {
      jsonData.push({equation: JSON.parse(window.localStorage.getItem('db-trial'))[iGET].equation, equationHTML: JSON.parse(window.localStorage.getItem('db-trial'))[iGET].equationHTML, answer: JSON.parse(window.localStorage.getItem('db-trial'))[iGET].answer});
    }
  }
  jsonData.push({equation: equationNormal, equationHTML: currentEquationHTML, answer: answer.toString()});
  localStorage.clear();
  const data = JSON.stringify(jsonData)
  window.localStorage.setItem('db-trial', data);

  currentEquationHTML = answer.toString()
  equationNormal = answer.toString()
  labelVar.innerHTML = currentEquationHTML;

  globalAdjustment()

  errorLabelBounds.style.backgroundColor = "transparent";
}

function newItemLabel(text) {
  if (currentEquationHTML == "00") {
    currentEquationHTML = ""
    let labelVar = document.getElementById('outputCase');
    labelVar.innerHTML = currentEquationHTML;
  }

  if (recordErrorMax == true) {
    alert("There are too many characters in this equation.")
  } else {
    let targetId = event.currentTarget.id;
    let labelVar = document.getElementById('outputCase');

    if (targetId == "rule-sine" || targetId == "rule-cosine" || targetId == "rule-tangent") {
      text += "("
      equationNormal += "Math." + text
    } else {
      text = text.replace(/\s/g, '');
      let specialCaseOperators = {"x": "*", "÷": "/", "%": "/100"};
      if (text in specialCaseOperators) {
        equationNormal += specialCaseOperators[text]
      } else {
        equationNormal += text
      }
    }

    currentEquationHTML += text
    currentEquationHTML = currentEquationHTML.replace(/\s/g, '');
    labelVar.innerHTML = currentEquationHTML;
    let labelWidth = labelVar.offsetWidth;
    adjustSizing(labelVar, labelWidth, text)
  }
}

function fadingIN(){
  var increment = 0.01;
  var opacity = 0;
  var instance = window.setInterval(function() {
      document.getElementById('history-backdrop').style.opacity = opacity
      opacity = opacity + increment;
      if(opacity > 1.01){
          window.clearInterval(instance);
      }
  }, 0.001)
}
function fadingOUT(){
  var increment = 0.01;
  var opacity = 1;
  var instance = window.setInterval(function() {
      document.getElementById('history-backdrop').style.opacity = opacity
      opacity = opacity - increment;
      if(opacity < 0){
          window.clearInterval(instance);
      }
  }, 0.001)
}

function resetDataForHistory() {
  localStorage.removeItem("loglevel:webpack-dev-server")
  if (localStorage.getItem("loglevel:webpack-dev-server") === null) {
    console.log("loglevel:webpack-dev-server not detected. :)")
  } else {
    localStorage.removeItem("loglevel:webpack-dev-server")
  }

  jsonData = []
  if (localStorage.length > 0) {
    for (var iGET = 0; iGET < JSON.parse(window.localStorage.getItem('db-trial')).length; iGET++) {
      jsonData.push({equation: JSON.parse(window.localStorage.getItem('db-trial'))[iGET].equation, equationHTML: JSON.parse(window.localStorage.getItem('db-trial'))[iGET].equationHTML, answer: JSON.parse(window.localStorage.getItem('db-trial'))[iGET].answer});
    }
  }
  var innerHTMLTable = ""// "<tr><th style='user-select:none;'>Equations</th><th style='user-select:none;'>Answers</th></tr>"
  for (var iData = 0; iData < jsonData.length; iData++) {
    var htmlEquationString = jsonData[iData].equationHTML.replace(/(<([^>]+)>)/gi, "");
    innerHTMLTable += "<tr><td class='table-cell-history'>" + htmlEquationString + "</td><td class='table-cell-answer'>" + jsonData[iData].answer.replace(/(.{7})..+/, "$1…") + "</td></tr>"
    // alert(jsonData[iData].equation)
  }
  document.getElementById("table-data-history").innerHTML = innerHTMLTable;

  if (localStorage.length == 0) {
    document.getElementById("table-history-data-empty").style.opacity = 1
  } else {
    document.getElementById("table-history-data-empty").style.opacity = 0
  }
}

export default {
  name: 'App',
  mounted() {
    //SET DATA FOR HISTORY TABLE
    // resetDataForHistory()

    //KEYBOARD LOG
    this._keyListener = function(e) {
      var letterReturn = ""
      switch (e.key) {
        case "0":
          letterReturn = "0";
          break;
        case "1":
          letterReturn = "1";
          break;
        case "2":
          letterReturn = "2";
          break;
        case "3":
          letterReturn = "3";
          break;
        case "4":
          letterReturn = "4";
          break;
        case "5":
          letterReturn = "5";
          break;
        case "6":
          letterReturn = "6";
          break;
        case "7":
          letterReturn = "7";
          break;
        case "8":
          letterReturn = "8";
          break;
        case "9":
          letterReturn = "9";
          break;
        case "+":
          letterReturn = "+";
          break;
        case "-":
          letterReturn = "-";
          break;
        case "*":
          letterReturn = "x";
          break;
        case "/":
          letterReturn = "÷";
          break;
        case "(":
          letterReturn = "(";
          break;
        case ")":
          letterReturn = ")";
          break;
        case "Enter":
          letterReturn = "enter";
          break;
        default:
          letterReturn = "";
      }
      
      if (letterReturn == "enter") {
        equalCommand()
      } else {
        newItemLabel(letterReturn)
      }
    };
    document.addEventListener('keydown', this._keyListener.bind(this));
  },
  methods: {
    select: function(event) {
      let targetId = event.currentTarget.id;
      if (targetId == "resetCalcultor") {
        currentEquationHTML = "00"
        equationNormal = ""
        maxFontSizeOutput = 50
        recordErrorMax = false
        let labelVar = document.getElementById('outputCase');
        labelVar.innerHTML = currentEquationHTML;
        var sizeHandleVar = maxFontSizeOutput.toString(); sizeHandleVar += "px";
        labelVar.style.fontSize = sizeHandleVar;

        let errorLabelBounds = document.getElementById("errorLabel")
        errorLabelBounds.style.backgroundColor = "transparent";
      
      } else if (targetId == "grid-plus-minus") {
        if (currentEquationHTML != "00") {
          if (currentEquationHTML.substring(0, 1) == "-") {
            currentEquationHTML = currentEquationHTML.substring(1)
            equationNormal = equationNormal.substring(1)
          } else {
            currentEquationHTML = "-" + currentEquationHTML
            equationNormal = "-" + equationNormal
          }
          let labelVar = document.getElementById('outputCase');
          labelVar.innerHTML = currentEquationHTML;
        }
      } else if (targetId == "grid-value-equal") {
        equalCommand()
      } else if (targetId == "history" || (targetId == "exit-history")) {
        resetDataForHistory()

        let historyView = document.getElementById("history-backdrop")
        let historyViewOpacity = historyView.style.opacity;
        if (historyViewOpacity <= 0.01) {
          fadingIN()
          historyView.style.zIndex = 950  //display = "block";
        } else {
          fadingOUT()
          historyView.style.zIndex = 0  //display = "none";
        }
      } else if (targetId == "clear-history") {
        jsonData = []
        localStorage.clear();
        resetDataForHistory()
      } else {
        newItemLabel(document.getElementById(targetId).innerText)
      }
    },
  },
}



      // } else if (targetId == "grid-backspace") {
      //   if (currentEquationHTML != "00") {
      //     maxFontSizeOutput = 50
      //     recordErrorMax = false
      //     let checkLength = equationNormal.substr(equationNormal.length - 4);
      //     if (checkLength == "sin(" || checkLength == "cos(" || checkLength == "tan(") {
      //       equationNormal = equationNormal.slice(0, -9)
      //       currentEquationHTML = currentEquationHTML.slice(0, -4)
      //     } else {
      //       equationNormal = equationNormal.slice(0, -1)
      //       currentEquationHTML = currentEquationHTML.slice(0, -1)
      //     }
      //     let labelVar = document.getElementById('outputCase');
      //     labelVar.innerHTML = currentEquationHTML;
      //     // for (var idCount = 0; idCount < currentEquationHTML.length; idCount++) {
      //     //   let text = currentEquationHTML.charAt(idCount);
      //     //   let labelWidth = labelVar.offsetWidth;
      //     //   if (labelWidth >= 350) {
      //     //     let str = window.getComputedStyle(labelVar, null).getPropertyValue('font-size');
      //     //     let int = parseInt(str)
      //     //     if (int <= 30) {
      //     //       currentEquationHTML = currentEquationHTML.slice(0, -1);
      //     //       currentEquationHTML += "<br/>"; currentEquationHTML += text
      //     //       labelVar.innerHTML = currentEquationHTML;
      //     //       let labelHeight = labelVar.offsetHeight;
      //     //       if (labelHeight > 204) { 
      //     //         currentEquationHTML = currentEquationHTML.slice(0, -1);
      //     //         labelVar.innerHTML = currentEquationHTML;
      //     //         let errorLabelBounds = document.getElementById("errorLabel")
      //     //         errorLabelBounds.style.backgroundColor = "#007bff";
      //     //         errorLabelBounds.innerHTML = "Too Many Characters"
      //     //         recordErrorMax = true;
      //     //       }
      //     //     } else {
      //     //       maxFontSizeOutput -= 3
      //     //       var sizeHandle3 = maxFontSizeOutput.toString(); sizeHandle3 += "px";
      //     //       labelVar.style.fontSize = sizeHandle3;
      //     //     }
      //     //   }
      //     // }

      //     globalAdjustment()
          
      //     if (currentEquationHTML == "") {
      //       currentEquationHTML = "00"
      //       equationNormal = ""
      //       labelVar.innerHTML = currentEquationHTML;
      //     }
      //   }
</script>

<style>
:root {
  --main: #007bff;
}
#app {
  font-family: Menlo;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  margin-top: 60px;
}

html, body {
  overflow: hidden;
}
body {
  background-color: #111;
  margin: 0;
  padding: 0;
}

.centered {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #222;
  width: 375px;
  height: 812px;
  border-radius: 30px;
}

.history-backdrop {
  background-color: #333;
  position: absolute;
  opacity: 0;
  bottom: 0;
  right: 0;
  left: 0;
  height: 425px;
  border-radius: 30px;
  z-index: 0;
}
.table-history-data-empty {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  font-size: 15px;
  user-select: none;
  opacity: 0;
}
.space-bar {
  position: absolute;
  background-color: gray;
  top: 5px;
  left: 50%;
  transform: translate(-50%, 0%);
  width: 45px;
  height: 4px;
  border-radius: 2px;
}
.exit-history {
  position: absolute;
  right: 15px;
  top: 20px;
  width: 38px;
  background-color: azure;
  border-radius: 27.5px;
  font-size: 20px;
  text-align: center;
  color: red;
  padding-top: 7px;
  padding-bottom: 7px;
  user-select:none;

}
.clear-history {
  position: absolute;
  right: 62px;
  top: 20px;
  width: 95px;
  background-color: azure;
  border-radius: 27.5px;
  font-size: 12px;
  text-align: center;
  color: black;
  padding-top: 12px;
  padding-bottom: 12px;
  user-select:none;
}
.history-label {
  position: absolute;
  left: 22px;
  top: 30px;
  /* font-weight: bold; */
  font-style: italic;
  font-size: 15px;
  user-select:none;
}
.table-data-history {
  position: absolute;
  bottom: 15px;
  left: 15px;
  right: 15px;
  height: 340px;
  /* width: 375px; */
  /* background-color: #111; */
  border-radius: 15px;
  overflow-y:scroll;
  display:block;
}
.table-cell-history {
  background-color: #444;
  padding: 15px;
  text-align: left;
  width: 220px;
  font-size: 13px;
  border-radius: 7px;
}
.table-cell-answer {
  font-size: 13px; 
  background-color: #666;
  width: 88px;
  border-radius: 7px;
  user-select:none;
}

/* Here is the output field */
.enterField {
  position: relative;
  background-color: azure;
  width: 375px;
  height: 320px;
  border-radius: 30px;
}
.outputLabel {
  position: absolute;
  bottom: 20px;
  right: 15px;
  max-width: 95%;
  max-height: 235px;
  font-size: 50px;
  color: black;
  /* background-color: burlywood; */
  text-align: right;
  /* user-select:none; */
}
.errorLabel {
  position: absolute;
  top: 45px;
  right: 15px;
  font-size: 12px;
  /* font-style: italic; */
  font-weight: bold;
  background-color: transparent;
  padding: 8px;
  border-radius: 12.5px;
  user-select:none;

  max-width: 375px;
  text-overflow: ellipsis;
  overflow: hidden;
}

/* Reset Button */
.resetCalcultor {
  position: absolute;
  left: 10px;
  margin-top: 10px;
  width: 50px;
  height: 50px;
  border-radius: 30px;
  background-color: azure;
  user-select:none;
}
.resetCalcultor p {
  position: absolute;
  top: 50%;
  left: 0;
  right: 0;
  margin-top: -9px;
  color: var(--main); 
  user-select:none;
}

/* ScrollView Options Bar */
.scrollmenu {
  position: absolute;
  margin-top: 10px;
  right: 15px;
  width: 180px;
  background-color: var(--main);
  overflow: auto;
  white-space: nowrap;
  border-radius: 15px;
  height: 50px;
}
.scrollmenu div {
  display: inline-block;
  color: white;
  text-align: center;
  padding: 12px;
  margin-left: 5px;
  margin-top: 5px;
  text-decoration: none;
  user-select: none;
}
::-webkit-scrollbar {
    width: 0px;
    background: transparent; /* make scrollbar transparent */
}

/* Number Pad (with decimal and equal output button) */
.grid-container-commands {
  display: grid;
  grid-gap: 9px;
  grid-template-columns: auto auto auto auto auto auto;
  position: absolute;
  margin-top: 60px;
  margin-left: 15px;
  margin-right: 15px;
  z-index: 49;
}
.grid-item-command {
  margin-top: 10px;
  width: 50px;
  height: 50px;
  border-radius: 30px;
  background-color: azure;
  user-select:none;
  text-align: center;
}
.grid-item-command p {
  color: var(--main); 
  user-select:none;
}

.long-rounded-button-clear {
  position: absolute;
  left: 15px;
  margin-top: 16.5px;
  width: 75px;
  background-color: azure;
  border-radius: 27.5px;
  font-size: 12px;
  text-align: center;
  color: red;
  padding-top: 12px;
  padding-bottom: 12px;
  user-select:none;
}
.long-rounded-button-history {
  position: absolute;
  left: 98px;
  margin-top: 16.5px;
  width: 75px;
  background-color: azure;
  border-radius: 27.5px;
  font-size: 12px;
  text-align: center;
  color: black;
  padding-top: 12px;
  padding-bottom: 12px;
  user-select:none;
}

/* Number Pad (with decimal and equal output button) */
.grid-container {
  display: grid;
  grid-template-columns: auto auto auto;
  /* padding: 10px; */
  position: absolute;
  bottom: 0;
  right: 0;
  left: 0;
  border-radius: 30px;
  background-color: rgba(0, 0, 0, 0.3);
  z-index: 50;
}
.grid-item {
  /* background-color: rgba(0, 0, 0, 0.1); */
  border: 1px solid rgba(0, 0, 0, 0.01);
  padding: 26px;
  font-size: 30px;
  text-align: center;
  user-select:none;
}
.grid-item-equal{
  background-color: var(--main);
  margin: 10px;
  padding-top: 15px;
  /* padding: 26.5px; */
  font-size: 35px;
  border-radius: 30px;
  text-align: center;
  color: white;
  user-select:none;
}
</style>
