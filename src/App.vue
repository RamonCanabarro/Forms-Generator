<template>
<div id="App">
<div class="container flex-flow">
  <button v-on:click="show = !show">
    Criar formulário
  </button>
  <div v-bind:style="bgc" class="item input-box">
  <input type="text" v-on:input="bgc.color = $event.target.value" placeholder="Type any colour name, rgb, hex or hsl. Example:Red"/>
  </div>
</div>
    <transition name="fade">
      <div  class="container"  v-bind:style="bgc" v-if="show">
        <p v-if="errors.length">
        <b>Por favor, corrija o(s) seguinte(s) erro(s):</b>
        <ul>
          <li v-for="error in errors" :key="error">{{ error }}</li>
        </ul>
      </p>
    </div>
    </transition>
  <transition name="fade">
  <div  v-bind:style="bgc" v-if="show" class="container flex-flow">
    <div class="item input-box">
      <label for="name">Campo</label>
      <input
        id="name"
        v-model="name"
        type="text"
        placeholder="Nome do campo"
      />
    </div> 
    <div class="item input-box">
      <label for="placeholder">Placeholder</label>
      <input
        id="placeholder"
        v-model="placeholder"
        type="text"
        placeholder="Placeholder desejado"
      />
    </div>  
    <div class="item input-select">
      <label for="tipo">Tipo de input</label>
      <select v-model="type">
      <option disabled value="">Escolha um item</option>
      <option value="text">Texto</option>
      <option value="password">Senha</option>
      <option value="email">Email</option>
      <option value="phone">Telefone</option>
    </select>
    </div>
  </div>
  </transition>
  <transition name="fade">
  <div v-if="show" class="container flex-flow">
    <div class="item">
      <button @click='sendForm'>Criar campo</button>
    </div>
  </div>
  </transition>
   <div v-bind:style="bgc">
    <Form :newInput="newInput" />
   </div>
</div>

</template>

<script>
import Form from "./components/Form.vue";

export default {
  name: "App",
  el: "#App",
  components: {
    Form,
  },
  data() {
    return {
      show: false,
      transitionName: "fade",
      errors: [],
      name: null,
      type: null,
      placeholder: null,
      newInput: {
        name: "",
        type: "",
      },
      bgc: {
        color: "",
      },
    };
  },
  methods: {
    toggleMenu: function () {
      this.menuOpen = !this.menuOpen;
    },
    checkForm() {
      this.errors = [];

      if (!this.name) {
        this.errors.push("O campo é obrigatório");
      }
      if (!this.type) {
        this.errors.push("O tipo é obrigatório");
      }
      if (!this.placeholder) {
        this.errors.push("O placeholder é obrigatório");
      }
      if (this.errors.length === 0) {
        return true;
      }
    },
    sendForm(e) {
      if (this.checkForm()) {
        this.newInput = {
          name: this.name,
          type: this.type,
          placeholder: this.placeholder,
        };
      }
      this.name = null;
      this.type = null;
      this.placeholder = null;
      e.preventDefault();
    },
  },
};
</script>

<style>
#App {
  background-color: black;
  border-radius: 10px;
  opacity: 0.5;
  padding: 1em;
  color: #fff;
}

#App:hover {
  opacity: 0.9;
}

body {
  font-family: "Poppins", sans-serif;
  min-height: 100vh;
  font-size: 1.5rem;
  background: #222222;
  background-color: black;
  background-repeat: no-repeat;
  background-position: 0 0;
  background-size: cover;
}
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
.input-select select {
  cursor: ponter;
  border-radius: 8px;
  background-color: #679e63;
  padding: 20px;
  width: 200px;
  box-shadow: 0 0 5px 0 #50894a;
  border: none;
  font-size: 20px;
  -webkit-appearance: button;
  appearance: button;
  outline: none;
}
.input-select label {
  font-size: 0.9rem;
  font-weight: bold;
  position: relative;
  text-transform: uppercase;
}
.input-select select option {
  padding: 30px;
}
.container {
  max-width: 50%;
  margin: 0 auto;
  display: flex;
}
.flex-flow {
  flex-flow: row wrap;
}
.item {
  flex: 1;
  margin: 5px;
  padding: 0 10px;
}
.input-box {
  display: grid;
  grid-auto-flow: row;
  grid-auto-rows: auto;
  grid-template-columns: 1fr;
  margin: 20px 0;
  position: relative;
}

.input-box label {
  font-size: 0.9rem;
  font-weight: bold;
  position: relative;
  text-transform: uppercase;
}

.input-box input {
  background: none;
  border: none;
  border-bottom: 1px solid #a9a9a9;
  color: #a9a9a9;
  font-size: 1.1rem;
  outline: none;
  padding: 10px 0;
  position: relative;
  width: 100%;
}
.input-box input:focus {
  border-image: linear-gradient(to right, #11998e, #38ef7d);
  border-image-slice: 1;
}
.input-box input:focus {
  font-weight: 700;
}
.footer-box {
  display: grid;
  grid-auto-flow: row;
  grid-auto-rows: auto;
  grid-template-columns: 1fr;
  margin: 10px 0;
  position: relative;
  text-align: center;
}
button {
  background-color: #679e63;
  border: none;
  border-radius: 8px;
  box-shadow: 0 0 5px 0 #50894a;
  display: grid;
  font-size: 1.1rem;
  font-weight: bold;
  padding: 15px 20px;
  width: 25%;
}
button:hover {
  cursor: pointer;
}
</style>
