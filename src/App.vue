<template>
  <div id="app">

    <div>
      <ul class="mini-container">
        <li v-for="(foto, index ) in fotos" class="container-lines lilines">
          <img :src="foto.url" alt="" class="mini-foto">
          <p>[Fig {{  parseInt(figuras[index] + 1) }}]</p>
          <button @click="apagar(foto)" class="botao">Apagar</button>
        </li>
      </ul>
    </div>

    <div>
      <!-- Modal Component -->
      <b-modal id="modalPrevent"
               ref="modal"
               title="Dados da imagem"
               @ok="handleOk"
               @shown="clearName">
        <form @submit.stop.prevent="handleSubmit">
          <b-form-input type="text"
            placeholder="Titulo: "
            v-model="fotos.titulo">
          </b-form-input>
          <p></p>
          <div class="image-handler">
            <b-form-file accept="image/jpeg, image/png, image/gif" v-model="file" ref="fileinput" class="mt-3" plain @change="handlertest"></b-form-file>
            <div class="mt-3">Selected file: {{file && file.name}}</div>
            <div class="imagemodal">
              <img :src="globalurl" />
            </div>
          </div>
          <p></p>
          <b-form-input type="text"
            placeholder="Fonte: "
            v-model="fotos.fonte">
          </b-form-input>
          <p></p>
          <b-form-input type="text"
            placeholder="Informações adicionais: "
            v-model="fotos.infos">
          </b-form-input>
          <p></p>
          <b>
          </b>
        </form>
      </b-modal>
    </div>

    <vue-editor id="editor"
      useCustomImageHandler
      @imageAdded="handleImageAdded"
      v-model="editorContent">
    </vue-editor>
  </div>

</template>

<script>
import {VueEditor, Quill} from 'vue2-editor'
import axios from 'axios'

export default {
  name: 'app',
  components: {
    VueEditor,

  },
  data () {
    return {
      editorContent: '<p><p contenteditable="false" class="fig" draggable="true" id="0" Ondrag><a class="atexto">[Fig 23]</a><a href="#" class="delete";">Delete</a></p></p><p>um outro paragrafo</p><p>um outro paragrafo</p><p>um outro paragrafo</p><p><p contenteditable="false" class="fig" draggable="true" id="1">[Fig 2]<a href="#" class="delete";">Delete</a></p></p><p>Test test 123</p>',
          fotos: [
            {
              url: 'https://dummyimage.com/800x800/000/fff&text=Fig1',
              nome: '[Fig 1]',
              id: "0",
              titulo: 'Figura 1',
              fonte: 'Ibope',
              infos: ''
            },
            {
              url: 'https://dummyimage.com/800x800/000/fff&text=Fig2',
              nome: '[Fig 2]',
              id: "1",
              titulo: 'Figura 2',
              fonte: 'Datafolha',
              infos: ''
            }
          ],
          globalID: 1,
          editorFotos: [],
          varFotos: [],
          element: null,
          figs: [],
          figuras: [],
          file: null,
          globalurl: ''


    }
  },


  watch:{


    editorContent(){


      for(var i = 0; i < document.getElementById('editor').getElementsByClassName('fig').length; i++) {
        document.getElementById('editor').getElementsByClassName('fig')[i].addEventListener('dragstart', this.dragstart_handler, false);
        document.getElementById('editor').getElementsByClassName('fig')[i].addEventListener('dragover', this.dragleave_handler, false);
      }

      for(var i = 0; i < document.getElementById('editor').getElementsByTagName('p').length; i++){
        document.getElementById('editor').getElementsByTagName('p')[i].addEventListener('dragenter', this.dragenter_handler, false);
      }
      for(var i = 0; i < document.getElementById('editor').getElementsByClassName('delete').length; i++){
        document.getElementById('editor').getElementsByClassName('delete')[i].addEventListener('click', this.deleteButton, false);
      }

      this.figsCount();

      for(var i = 0; i < this.fotos.length; i++){
        if(document.getElementById('editor').getElementsByClassName('fig')[i] !== undefined){
          this.figs[i] = document.getElementById('editor').getElementsByClassName('fig')[i].id
          var figsIndex = [];
          figsIndex[i] = this.fotos[i].id;
          //console.log(this.figs[i]);
          //console.log(this.fotos[i].id);
          //console.log(this.figs.indexOf(this.fotos.id[i]));
          console.log(this.figs.indexOf(figsIndex[i]));
        }else{
          if(this.figs.indexOf(figsIndex[i]) == -1){
          console.log("swplicse");
          this.fotos.splice(i,1);
        }
        }
      }


    }

  },

  mounted(){
    this.figsCount();
    for(var i = 0; i < document.getElementById('editor').getElementsByClassName('fig').length; i++) {
      document.getElementById('editor').getElementsByClassName('fig')[i].addEventListener('dragstart', this.dragstart_handler, false);
      document.getElementById('editor').getElementsByClassName('fig')[i].addEventListener('dragover', this.dragleave_handler, false);
    }

    for(var i = 0; i < document.getElementById('editor').getElementsByTagName('p').length; i++){
      document.getElementById('editor').getElementsByTagName('p')[i].addEventListener('dragenter', this.dragenter_handler, false);
    }
    var self = this;
    var customButton = document.querySelector('.ql-image');
    customButton.addEventListener('click', function() {
    var modal1 = document.getElementById("modalPrevent");
    self.showModal();
    });
  },

  methods: {

    showModal () {
      this.$refs.modal.show()
    },

    clearName () {
  this.fotos.url = '';
  this.fotos.titulo = '';
  this.fotos.fonte= '';
  this.fotos.infos = '';
  this.$refs.fileinput.reset();
  this.globalurl = '';
},
handleOk (evt) {
  // Prevent modal from closing
  evt.preventDefault()
  if (!this.fotos.titulo) {
    alert('Por favor, digite o título da imagem');
  }
  if(!this.fotos.fonte){
    alert('Por favor, digite a fonte da imagem');
  }
  if(!this.file){
    alert('Por favor, coloque uma imagem');
  }


    else{
      this.handleSubmit(this.fotos.url, this.fotos.titulo, this.fotos.fonte, this.fotos.infos)
  }

},
handleSubmit (url, titulo, fonte, infos) {
  this.insertFoto();

  for(var i = 0; i < document.getElementById('editor').getElementsByClassName('fig').length; i++) {
    if(document.getElementById('editor').getElementsByClassName('fig')[i].id == "" ){
      document.getElementById('editor').getElementsByClassName('fig')[i].setAttribute("id", this.globalID.toString());
      document.getElementById('editor').getElementsByClassName('fig')[i].setAttribute("draggable", "true");
  }
}
  this.fotos.push({
    url: this.globalurl,
    nome: "[Fig"+ (this.globalID+1).toString()+"]",
    id: this.globalID.toString(),
    titulo: titulo,
    fonte: fonte,
    infos: infos
  })
  this.figsCount();

  //this.fotos.url='', this.fotos.nome='', this.fotos.id='', this.fotos.titulo, this.fotos.fonte='', this.fotos.indos='')
  this.clearName()
  this.$refs.modal.hide()
},



    figsCount: function(){
      var current = 1;
      var figsfigsId = [];
      document.querySelectorAll('.ql-editor .fig').forEach(function (figure, index) {
        figure.childNodes[0].textContent = '[Fig ' + current + ']';
        figsfigsId[index] = figure.id;
        current++;
      });

      var thFotos = [];
      for(var i = 0; i < figsfigsId.length; i++){
        thFotos[i] = this.fotos[i].id;
        //figuras[i] = thFotos.indexOf(figsfigsId[i]);
        this.figuras[i] = figsfigsId.indexOf(thFotos[i]);
      }
    },

    deleteButton: function(e){
      e.preventDefault();
      console.log("clicked");
      console.log(e.srcElement.parentNode.id + "this id");
      e.srcElement.parentNode.remove(e.srcElement.parentNode);
      this.fotos.splice(e.srcElement.parentNode.id,1);
      this.figsCount();
},


insertFoto() {
  var elemento = document.createElement("p");
  var textNode = document.createElement("a"); //tentativa 1
  elemento.appendChild(textNode);
  elemento.className = 'fig';
  elemento.setAttribute("contenteditable", "false");
  elemento.setAttribute("draggable", "true");
  elemento.appendChild(document.createTextNode("[Fig"+ (this.globalID+1).toString()+"]"));
  document.querySelector(".ql-editor").appendChild(elemento);
  textNode.href = "#";
  textNode.className = "delete";
  textNode.innerHTML = "Delete";
  document.querySelector(".fig:last-child").appendChild(textNode);
  //textNode.innerHTML = "[Fig"+ (this.globalID+1).toString()+"]"; //tentativa 1
  console.log(textNode);
},


dragstart_handler: function (event){

  this.element = event.srcElement;

},

dragenter_handler: function (event) {
  if (this.element.parentNode && event.srcElement != this.element && event.srcElement.className != "fig") {
    this.element.parentNode.removeChild(this.element);
		event.srcElement.parentNode.insertBefore(this.element, event.srcElement.nextSibling);

  }
},

arrayEditorFotos(){
  for(var i = 0; i < document.getElementById('editor').getElementsByClassName('fig').length; i++) {
  this.editorFotos[i] = document.getElementById('editor').getElementsByClassName('fig')[i].id;
  if(this.editorFotos.length > document.getElementById('editor').getElementsByClassName('fig').length){
    this.editorFotos.pop();
  }
}
},

arrayvarFotos(){
  for(var i = 0; i < this.fotos.length; i++) {
  this.varFotos[i] = this.fotos[i].id;
  }
},

    calcularFotos(){
      if(document.getElementById('editor').getElementsByTagName('img').length != null){
      for(var i = 0; i < document.getElementById('editor').getElementsByTagName('img').length; i++) {
        fotos.push(i);
    }
  }
    },

    handlertest(e){
      var files = e.target.files;

      if (files[0]) {
	var img = new Image()

	img.addEventListener('load', function () {
		console.log(this.width + 'x' + this.height)
	})

	img.setAttribute('src', URL.createObjectURL(files[0]))
}

      if(files.width > 900){
        alert("Imagem com largura maior que 900px");
      }
      const CLIENT_ID = '993793b1d8d3e2e'
      var formData = new FormData();
      formData.append('image', files[0])

      axios({
        url: 'https://api.imgur.com/3/image',
        method: 'POST',
        headers:{
          'Authorization': 'Client-ID ' + CLIENT_ID
        },
        data: formData
      })
      .then((result) => {
        console.log(result);
        let url = result.data.data.link
        this.globalurl = url;
        //Editor.insertEmbed(cursorLocation, 'image', url);

        //Editor.insertEmbed(cursorLocation, 'image', url); // inserir no final
        this.globalID++
        console.log(url);

        //}
        //console.log(this.fotos[0]);

      })
      .catch((err) => {
        console.log(err);
      })
    },

    handleImageAdded(file, Editor, cursorLocation) {
      file = e.target.files;
      console.log(file);
      const CLIENT_ID = '993793b1d8d3e2e'
      var formData = new FormData();
      formData.append('image', file)


      axios({
        url: 'https://api.imgur.com/3/image',
        method: 'POST',
        headers:{
          'Authorization': 'Client-ID ' + CLIENT_ID
        },
        data: formData
      })
      .then((result) => {
        console.log(result);
        let url = result.data.data.link
        //Editor.insertEmbed(cursorLocation, 'image', url);
        let range = Editor.getSelection(true); //inserir no final

        //Editor.insertEmbed(cursorLocation, 'image', url); // inserir no final
        this.globalID++


        console.log(url);
        for(var i = 0; i < document.getElementById('editor').getElementsByClassName('fig').length; i++) {
          if(document.getElementById('editor').getElementsByClassName('fig')[i].id == "" ){
            document.getElementById('editor').getElementsByClassName('fig')[i].setAttribute("id", this.globalID);
            document.getElementById('editor').getElementsByClassName('fig')[i].setAttribute("draggable", "true");
        }

      }
        //}
        //console.log(this.fotos[0]);

      })
      .catch((err) => {
        console.log(err);
      })
  },

  apagar(foto){
    console.log(foto.id+"inicio");
    var elem = document.getElementById(foto.id);
    elem.parentNode.removeChild(elem);
    var indice = this.fotos.indexOf(foto);
    this.fotos.splice(indice, 1);
    this.figsId = [];
  },
  getImages(contentId) {
    var content = document.getElementById(contentId);
    // only one image, just return an item; or you can return an array
    if (content) return document.getElementsByTagName('img')[0];
}
  }
}
</script>

<style>
@import "https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/styles/default.min.css";

#editor {
  height: 400px;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /*margin-top: 60px;*/
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
  padding-bottom: 3em;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
  width: 100%;
}

a {
  color: #42b983;
}

.mini-container{
  display: inline-block;
}

.mini-foto{
  width: 5%;
  float: left;
}

.container-lines > li{
  width: 100%
}

.container-lines > p{
    float: left;
    margin-left: 20px;
}

.botao{
  float: left;
  margin-left: 20px;
  margin-top: 15px;
}

.fig {
  background-color: lightblue;
  display: block;
  width: 100%;
  text-align: center;
  cursor: move;
  overflow: hidden;
  height: 30px;
}
.fig:hover a.delete { display:block; }

a.delete {
  display: none;
  position: relative;
  top: -18px;
  right: 0;
  left: 96%;
  width: 30px;
  height: 30px;
  text-indent: -80px;
  background: red;
}

.imagemodal img{
  width: 50%;
}

</style>
