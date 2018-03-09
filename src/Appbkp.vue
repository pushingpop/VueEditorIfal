<template>
  <div id="app">

    <div>
      <ul class="mini-container">
        <li v-for="(foto, index ) in fotos" class="container-lines lilines">
          <img :src="foto.url" alt="" class="mini-foto">
          <p v-if="figs[index] !== undefined">[Fig {{  (parseInt(figs[index].id) + 1).toString() }}]</p>
          <p>[Fig {{  parseInt(figuras[index] + 1) }}]</p>

          <button @click="apagar(foto)" class="botao">Apagar</button>
        </li>

      </ul>



    </div>

    <div class="imagem-min" v-model="editorContent">

    </div>
    <vue-editor id="editor"
      useCustomImageHandler
      @imageAdded="handleImageAdded"
      v-model="editorContent">
    </vue-editor>
  </div>

</template>

<script>
import {VueEditor} from 'vue2-editor'
import axios from 'axios'

export default {
  name: 'app',
  components: {
    VueEditor,
  },
  data () {
    return {
      editorContent: '<p><p contenteditable="false" class="fig" draggable="true" id="0" Ondrag>[Fig 1]<a href="#" class="delete";">Delete</a></p></p><p>um outro paragrafo</p><p>um outro paragrafo</p><p>um outro paragrafo</p><p><p contenteditable="false" class="fig" draggable="true" id="1">[Fig 2]</p></p><p>Test test 123</p>',
          fotos: [
            {
              url: 'https://dummyimage.com/800x800/000/fff&text=Fig1',
              nome: '[Fig 1]',
              id: "0"
            },
            {
              url: 'https://dummyimage.com/800x800/000/fff&text=Fig2',
              nome: '[Fig 2]',
              id: "1"
            }
          ],
          globalID: 1,
          editorFotos: [],
          varFotos: [],
          element: null,
          figs: [],
          figuras: [],


    }
  },


  watch:{


    editorContent(){


/*
      this.figs = document.getElementById('editor').getElementsByClassName('fig');

      for (var i = 0; i < this.figs.length; i++){
        this.figs[i].textContent = "[Fig " + [i] + "]"
      }
*/
/*
      var abc = [];
      for(var i = 0; i < this.figs.length; i++){
        if(this.figs[i] !== null){
        this.figsId[i] = this.figs[i].id;
        abc[i] = (document.getElementById('editor').getElementsByClassName('fig')[i].id).toString();
        //console.log(this.figs[i]);
                console.log(this.figsId.indexOf(abc[i]));
                //console.log(abc[i]+"abc");
                console.log(this.figs);
                //console.log(abc.indexOf([i].toString())+"indice abc");
                console.log(this.figsId);
      }
    }

    for(var i = 0; i < abc.length; i++){
        //console.log(abc.indexOf(abc[i].toString())+"indice abc");
        //console.log(abc[i]+"abc");
        //console.log(abc.includes([i]));
        //this.figsId[i] = parseInt(this.figsId[i]);
        //console.log(this.figsId[i]);
}
*/
    //  var validList = document.getElementById('editor').getElementsByTagName('img').filter((id) => {
      //  return !fotos.has(id);
      if (document.getElementById('editor').getElementsByClassName('fig').length ==0){
        this.fotos.splice(0,this.fotos.length);
        console.log("SPLICE");
      }


    //console.log(document.getElementById('editor').getElementsByTagName('img').length+"imgLength");
    //console.log(this.editorFotos+" editorFotos");
/*
    this.arrayEditorFotos();
    this.arrayvarFotos();

      let difference = this.varFotos.filter(x => !this.editorFotos.includes(x));
      console.log(difference.length +"Diff Length");
      if(difference.length > 0){
      //console.log(difference.toString());
      var diffString = difference.toString().charAt(0);

      console.log("string: " + diffString +"diff");
      //console.log(this.fotos);
      var indexx = this.varFotos.indexOf(diffString);
      console.log(indexx);


      //console.log(indexx);
      this.fotos.splice(indexx,1); //pegar onde a diferença ta no id de this.fotos
      difference.splice(0, difference.length);
      this.varFotos.pop();
    }
    */

      for(var i = 0; i < document.getElementById('editor').getElementsByClassName('fig').length; i++) {
        document.getElementById('editor').getElementsByClassName('fig')[i].addEventListener('dragstart', this.dragstart_handler, false);
        document.getElementById('editor').getElementsByClassName('fig')[i].addEventListener('dragover', this.dragleave_handler, false);
      }

      for(var i = 0; i < document.getElementById('editor').getElementsByTagName('p').length; i++){
        document.getElementById('editor').getElementsByTagName('p')[i].addEventListener('dragenter', this.dragenter_handler, false);
      }
      for(var i = 0; i < document.getElementById('editor').getElementsByClassName('delete').length; i++){
        document.getElementById('editor').getElementsByClassName('delete')[i].addEventListener('click', this.deleteButton, false);
        console.log("clickclick");
      }

      this.figsCount();

    }

  },


  methods: {

    figsCount: function(){
      var current = 1;
      var figsfigsId = [];
      var figsId = [];
      document.querySelectorAll('.ql-editor .fig').forEach(function (figure, index) {
        figure.textContent = '[Fig ' + current + ']';
        figsfigsId[index] = figure.id;
        console.log(figsfigsId);
        //figsId[index] = figsfigsId.indexOf(this.fotos[index].id);
        current++;
        //console.log(current);
      });
      var thFotos = [];

      for(var i = 0; i < figsfigsId.length; i++){

        thFotos[i] = this.fotos[i].id;
        console.log(thFotos+" th");
        //figuras[i] = thFotos.indexOf(figsfigsId[i]);
        this.figuras[i] = figsfigsId.indexOf(thFotos[i]);
      }
        console.log(this.figuras+" figuras");
    },

    deleteButton: function(e){
      e.preventDefault();
      console.log("clicked");
      console.log(e.srcElement.parentNode.id + "this id");
      e.srcElement.parentNode.remove(e.srcElement.parentNode);
      this.fotos.splice(e.srcElement.parentNode.id,1);
      /*
      $('a.delete').on('click',function(e){
      e.preventDefault();
      imageID = $(this).closest('.image')[0].id;
      alert('Now deleting "'+imageID+'"');
      $(this).closest('.image')
          .fadeTo(300,0,function(){
              $(this)
                  .animate({width:0},200,function(){
                      $(this)
                          .remove();
                  });
          });
  });
  */
},

created: function () {
  document.addEventListener('dragstart', this.dragstart_handler, false);
},

insertFoto() {
  var elemento = document.createElement("p");
  elemento.className = 'fig';
  elemento.setAttribute("contenteditable", "false");
  elemento.setAttribute("draggable", "true");
  elemento.appendChild(document.createTextNode("[Fig"+ (this.globalID+1).toString()+"]"));
  //parseInt(figs[index].id) + 1).toString()
  //document.getElementsByClassName('ql-editor').appendChild(elemento);
  document.querySelector(".ql-editor").appendChild(elemento);
},

dragleave_handler: function(event){
  var figArray = document.getElementById('editor').getElementsByClassName('fig');
  //console.log(figArray);
  var figQ = [];
  for(var i = 0; i < figArray.length; i++) {
      figQ[i] = figArray[i].id;
  }
  var figId = figQ.indexOf(this.element.id);
  this.element.textContent = "[Fig "+(parseInt(figId)+1)+"]"
},

dragstart_handler: function (event){

  this.element = event.srcElement;

  //console.log(this.element.textContent);
},

dragenter_handler: function (event) {
  //console.log(event.srcElement);
  if (this.element.parentNode && event.srcElement != this.element && event.srcElement.className != "fig") {
    this.element.parentNode.removeChild(this.element);
		event.srcElement.parentNode.insertBefore(this.element, event.srcElement.nextSibling);

  }
/*
  var figArray = document.getElementById('editor').getElementsByClassName('fig');
  //console.log(figArray);
  var figQ = [];
  for(var i = 0; i < figArray.length; i++) {
      figQ[i] = figArray[i].id;
  }
  var figId = figQ.indexOf(this.element.id);
  var srcId = figQ.indexOf(event.srcElement.id);

  this.element.textContent = "[Fig "+(parseInt(figId)+1)+"]";
  if(event.srcElement.className == "fig"){
  event.srcElement.textContent = "[Fig "+(parseInt(srcId)+1)+"]";
  console.log(event.srcElement.id  + "srcID");
  }
  */
},

arrayEditorFotos(){
  for(var i = 0; i < document.getElementById('editor').getElementsByClassName('fig').length; i++) {
  this.editorFotos[i] = document.getElementById('editor').getElementsByClassName('fig')[i].id;
  if(this.editorFotos.length > document.getElementById('editor').getElementsByClassName('fig').length){
    this.editorFotos.pop();
  }
  //this.varFotos[i] = this.fotos[i].id;
  //console.log(document.getElementById('editor').getElementsByTagName('img')[i].id);

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

    handleImageAdded(file, Editor, cursorLocation) {

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
        this.insertFoto();
        this.fotos.push({url:url, nome:"nome", id: this.globalID.toString(),});
        console.log(url);
        //if(document.getElementById('editor').getElementsByTagName('img').id === undefined ){
        for(var i = 0; i < document.getElementById('editor').getElementsByClassName('fig').length; i++) {
          if(document.getElementById('editor').getElementsByClassName('fig')[i].id == "" ){
            //console.log(document.getElementById('editor').getElementsByTagName('img')[i].id);
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
    //console.log(this.fotos[2].id);
    //var indice = this.fotos.indexOf(foto);
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
}
.fig:hover a.delete { display:block; }

a.delete {
  display:none;position:absolute;top:0;right:0;width:30px;height:30px;text-indent:-999px;background:red;
}

</style>
