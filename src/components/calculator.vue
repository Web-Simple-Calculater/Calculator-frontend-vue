<template>
  <div class="calculator">
    <div class="display" id="top">{{ operation }}</div>
    <div class="display">{{ current || "0" }}</div>
    <input type="button" value="%" @click="percent();operate();" class="btn high" />
    <input type="button" value="C" @click="clear" class="btn  high" />
    <input type="button" value="CE" @click="clear" class="btn high" />
    <input type="button" value="D" @click="deleting" class="btn high" />
    <input type="button" value="1/x" @click="fraction();operate();" class="btn high"/>
    <input type="button" value="x^2" @click="squere();operate();" class="btn high" />
    <input type="button" value="sqr" @click="root();operate();" class="btn high" />
    <input type="button" value="÷" @click="divide" class="btn high" />
    <input type="button" value="7" @click="append('7')" class="btn" />
    <input type="button" value="8" @click="append('8')" class="btn" />
    <input type="button" value="9" @click="append('9')" class="btn" />
    <input type="button" value="x" @click="times" class="btn high" />
    <input type="button" value="4" @click="append('4')" class="btn" />
    <input type="button" value="5" @click="append('5')" class="btn" />
    <input type="button" value="6" @click="append('6')" class="btn" />
    <input type="button" value="-" @click="minus" class="btn high" />
    <input type="button" value="1" @click="append('1')" class="btn" />
    <input type="button" value="2" @click="append('2')" class="btn" />
    <input type="button" value="3" @click="append('3')" class="btn" />
    <input type="button" value="+" @click="add" class="btn high" />
    <input type="button" value="+/-" @click="sign" class="btn" />
    <input type="button" value="0" @click="append('0')" class="btn" />
    <input type="button" value="." @click="dot" class="btn dot" />
    <input type="button" value="=" @click="quelify();equal();" class="btn eqal" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      first: "",
      second: "",
      current: "",
      operator: "",
      operation: "",
      single: "",
      operatorClicked: false,
      operatorOne: false,
      finish: false
    };
  },
  methods: {
    root() {
      this.single = "root";
    },
    squere() {
      this.single = "squere";
    },
    fraction() {
      this.single = "fraction";
    },
    percent() {
      this.single = "percent";
    },
    deleting() {
      this.current = this.current.slice(0, -1);
    },
    clear() {
      this.current = "";
      this.operation = "";
    },
    sign() {
      this.current =
        this.current.charAt(0) === "-"
          ? this.current.slice(1)
          : `-${this.current}`;
    },
    append(number) {
      if(this.finish){
        this.operation="";
        this.operatorOne=false;}
      if (this.operatorClicked || this.finish) {
        this.first=this.current;
        this.current = "";
        this.operatorClicked = false;
        this.finish = false;
      }
      this.current = `${this.current}${number}`;
    },
    dot() {
      if (this.current.indexOf(".") === -1) {
        this.append(".");
      }
    },
    divide() {
      if (!this.operatorClicked && this.current !== "" && !this.operatorOne) {
        this.operator = "divide";
        this.setFirst();
        this.operatorOne = true;
        this.operation = `${this.operation}${"÷"}`;
      }else if (this.operatorOne && this.current !== ""){
        this.quelify();
        this.operator = "divide";
        this.operatorClicked = true;
        this.operation = `${this.operation}${this.second}${"÷"}`;
      }
    },
    times() {
      if (!this.operatorClicked && this.current !== "" && !this.operatorOne) {
        this.operator = "multiply";
        this.setFirst();
        this.operatorOne = true;
        this.operation = `${this.operation}${"x"}`;
      }else if (this.operatorOne && this.current !== "" ){
        this.quelify();
        this.operator = "multiply";
        this.operatorClicked = true;
        this.operation = `${this.operation}${this.second}${"x"}`;
      }
    },
    minus() {
      if (!this.operatorClicked && this.current !== "" && !this.operatorOne) {
        this.operator = "minus";
        this.setFirst();
        this.operatorOne = true;
        this.operation = `${this.operation}${"-"}`;
      }else if (this.operatorOne && this.current !== ""){
        this.quelify();
        this.operator = "minus";
        this.operatorClicked = true;
        this.operation = `${this.operation}${this.second}${"-"}`;
      }
    },
    add() {
      if (!this.operatorClicked && this.current !== "" && !this.operatorOne) {
        this.operator = "add";
        this.setFirst();
        this.operatorOne = true;
        this.operation = `${this.operation}${"+"}`;
      }else if (this.operatorOne && this.current !== ""){
        this.quelify();
        this.operator = "add";
        this.operatorClicked = true;
        this.operation = `${this.operation}${this.second}${"+"}`;
      }
    },
    setFirst() {
      this.first = this.current;
      this.operation = `${this.operation}${this.first}`;
      this.operatorClicked = true;
    },
    operate() {
      if (this.current !== "") {
        fetch("http://localhost:8085/" + this.single + "/" + this.current, {method: "get"})
          .then(response => {
            return response.json();
          })
          .then(data => {
            this.current = data;
          });
        
      }
    },
    quelify(){
      if (!this.finish  &&  this.current !== "" ) {
        this.second = this.current;
        fetch("http://localhost:8085/" +this.first + "/" + this.operator +"/" + this.second,{ method: "get" })
          .then(response => {
            return response.text();
          })
          .then(data => {
            this.current = data;
          });
          
      }
    },
    equal() {
      if (!this.finish  &&  this.current !== "" ) {
        this.first = "";
        this.finish = true;
        this.operation = `${this.operation}${this.second}`;
      }
    }
  }
};
</script>

<style scoped>
.calculator {
  margin: 0 auto;
  border: 3px solid black;
  width: 400px;
  height: 600 px;
  font-size: 40px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(75px, auto);
}

.display {
  grid-column: 1 / 5;
  background-color: #e4e1e1;
  color: #000;
  text-align: right;
}
#top {
  font-size: 20px;
  color: #6566ee;
}

.btn {
  background-color: #ffffff;
  border: 1px solid #999;
  font-size: 35px;
}

.high {
  background-color: #b8b4b4;
}

.eqal {
  background-color: #6566ee;
}
</style>
