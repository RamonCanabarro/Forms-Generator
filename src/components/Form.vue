<template>
  <div id="app" class="modal-vue">
    <div class="overlay" v-if="showModal" @click="showModal = false"></div>
    <div class="modal" v-if="showModal">
      <div>
        <button class="close" @click="showModal = false">X</button>
      </div>
      <table>
        <tr>
          <th v-for="(data, k) in datas" :key="k">{{inputs[k].name}}</th>
        </tr>
        <tr v-if="datas.length">
          <td v-for="(data) in datas" :key="data">{{data}}</td>
        </tr>
      </table>
    </div>
  </div>
  <form
    v-if="inputs[0].name.length !== 0 && inputs[0].type.length !== 0">
  <div class="container" >
  <p v-if="errors.length">
        <b>Por favor, corrija o(s) seguinte(s) erro(s):</b>
        <ul>
          <li v-for="error in errors" :key="error">{{ error }}</li>
        </ul>
      </p>
  </div>
    <div class="container flex-flow">
      <div v-for="(input, i) in inputs" :key="i">
        <div
          v-if="input.name === 0 && input.type.length === 0" class="input-box item" >
          <p class="item">Nenhum formulário criado</p>
        </div>
        <div v-else-if="input.name.length && input.type === 'phone'" class="input-box item">
          <label for="name">{{ input.name }}</label>
            <input
            id="tel"
             type="text" v-model="datas[i]"
             :placeholder="input.placeholder"
             v-on:input="phoneMask">
            <a @click="removeInput(i)">X</a>
        </div>
        <div v-else class="input-box item">
          <label for="name">{{ input.name }}</label>
          <input
            id="name"
            v-model="datas[i]"
            :type="input.type"
            :placeholder="input.placeholder"
          />
          <a @click="removeInput(i)">X</a>
        </div>
      </div>
    </div>
    <div class="container flex-flow">
      <button @click="checkForm">Enviar</button>
    </div>
  </form>

  <div
    v-if="inputs[0].name.length !== 0 && inputs[0].type.length !== 0"
    class="container flex-flow"
  >
    <h1>Escolha o plano de fundo com um click</h1>
    <span v-for="(image, index) in images" :key="index" class="thumbSpan">
      <img v-bind:src="image" class="thumb" v-on:click="currentImage = index" />
    </span>
  </div>
  <div class="footer-box">
    <p>Ramon S Canabarro - 2020</p>
  </div>
</template>

<script>
import axios from "axios";
import VMasker from "vanilla-masker";

export default {
  name: "Form",
  props: {
    newInput: Object,
  },
  data() {
    return {
      showModal: false,
      inputs: [{ name: "", type: "", placeholder: "" }],
      datas: [],
      errors: [],
      posts: null,
      images: [],
      arraySize: 0,
      currentImage: 0,
    };
  },
  methods: {
    validEmail(email) {
      var re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(email);
    },
    phoneMask() {
      var telMask = ["(99) 9999-99999", "(99) 99999-9999"];
      var tel = document.querySelector("#tel");
      VMasker(tel).maskPattern(telMask[0]);
      tel.addEventListener(
        "input",
        this.inputHandler.bind(undefined, telMask, 14),
        false
      );
    },
    inputHandler(masks, max, event) {
      var c = event.target;
      var v = c.value.replace(/\D/g, "");
      var m = c.value.length > max ? 1 : 0;
      VMasker(c).unMask();
      VMasker(c).maskPattern(masks[m]);
      c.value = VMasker.toPattern(v, masks[m]);
    },
    removeInput(input) {
      if (this.inputs.length === 1)
        this.inputs = [{ name: "", type: "", placeholder: "" }];
      else this.inputs.splice(input, 1);
    },
    checkForm(e) {
      this.errors = [];
      if (this.datas.length !== 0) {
        this.inputs.forEach((input, i) => {
          if (input.type === "email") {
            if (!this.validEmail(this.datas[i])) {
              this.errors.push("Utilize um e-mail válido.");
            }
          }
          if (!this.datas[i])
            this.errors.push(`O campo ${input.name} é obrigatório`);
        });
      } else {
        this.errors.push("Preencha todo os campos");
      }
      if (this.errors.length === 0) this.click();
      e.preventDefault();
    },
    click() {
      this.showModal = !this.showModal;
    },
  },
  watch: {
    newInput() {
      if (this.inputs[0].name.length !== 0 && this.inputs[0].type.length !== 0)
        this.inputs.push(this.newInput);
      else this.inputs = [this.newInput];
    },
    posts() {
      this.arraySize = this.posts.length;
      for (var i = 0; i < this.arraySize; i++) {
        var x = this.posts[i].content.indexOf('href="');
        var y = this.posts[i].content.indexOf('"', x + 6);
        this.images.push(this.posts[i].content.substring(x + 6, y));
      }
      document.body.style.backgroundImage =
        "url('" + this.images[this.currentImage] + "')";
    },
    currentImage() {
      document.body.style.backgroundImage =
        "url('" + this.images[this.currentImage] + "')";
    },
  },
  mounted() {
    axios
      .get(
        "https://www.googleapis.com/blogger/v3/blogs/3412726727067752443/posts?key=AIzaSyDMDCj74G_V9RzYRUXvQLsK2AWcob7FsKw"
      )
      .then((response) => (this.posts = response.data.items));
  },
};
</script>

<style>
.thumb {
  width: 10em;
  margin-left: 5px;
  margin-right: 5px;
}

.modal-vue .overlay {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-vue .modal {
  position: relative;
  width: 100%;
  z-index: 9999;
  margin: 0 auto;
  padding: 20px 30px;
  background-color: #fff;
  color: black;
}
a {
  cursor: pointer;
}
.modal-vue .close {
  position: absolute;
  top: 10px;
  right: 10px;
}
table {
  width: 50%;
}
table,
th,
td {
  border: 1px solid black;
  border-collapse: collapse;
}
th,
td {
  padding: 15px;
  text-align: left;
}
tr:nth-child(even) {
  background-color: #eee;
}
tr:nth-child(odd) {
  background-color: #fff;
}
th {
  background-color: #679e63;
  color: white;
}
</style>
