<template>
  <p v-if="tabela">Consulta por nome tem prioridade</p>
  <Input v-if="tabela" type="text" placeholder="Pesquisar video pelo nome..." v-on:mudar="mudarNome" />
  <Input v-if="tabela" type="number" placeholder="Pesquisar video pelo id..." v-on:mudar="mudarId" />
  <h1>{{this.tituloPrincipal}}</h1>
  <table v-if="tabela">
  <thead>
    <th>Nome</th>
    <th>Nota</th>
    <th>Descrição</th>
    <th>Ações</th>
  </thead>
  <tbody>
    <Tr v-on:editar="editarVideoMostrar" v-on:deletar="deletarVideo" v-for="video in videos" :video="video" :key="video.id" />
  </tbody>
  </table>
  <Button funcao="Mostrar tabela de vídeos cadastrados" v-on:clicar="mostrarTabela" />
  <br/>
  <Button funcao="Cadastrar vídeos" v-on:clicar="cadastrarVideoMostrar" />
  <h2>{{tituloSecundario}}</h2>
  <form v-if="form" @submit.prevent="salvarVideo">
     <label for="inputNome">Nome:</label><input v-model="this.videoForm.nome" id="inputNome" placeholder="Nome do vídeo" type="text"> <br/>
     <label  for="inputNota">Nota:</label><input v-model="this.videoForm.nota" id="inputNota" placeholder="Nota" type="number" min=0 max=5><br/>
     <label  for="inputDescricao">Descricao:</label><textarea v-model="this.videoForm.descricao" placeholder="Descreva o vídeo..." id="inputDescricao" type="textarea" rows="4" cols="50"/><br/>
     <input id="submitForm" type="submit" value="Enviar">
  </form>
</template>

<script>
import axios from 'axios'

import Tr from './components/Tr.vue'
import Button from './components/Button.vue'
import Input from './components/Input.vue'

export default {
  name: 'App',
  components: {
    Tr,
    Button,
    Input
  },
  data() {
    return {
      pesquisaNome: '',
      pesquisaId: '',
      videos: {},
      videosOriginal: {},
      videosId: [],
      videoForm: {'nome: ': '','nota': '', 'descricao':''},
      form: 0,
      tituloPrincipal: '',
      tituloSecundario: '',
      tabela: 0
    }
  },
  methods: {
    mudarId(valor){
      this.pesquisaId = valor
    },
    mudarNome(valor){
      this.pesquisaNome = valor
    },
    mostrarTabela(){
      this.tabela = !this.tabela
      this.tituloPrincipal = this.tituloPrincipal?'':'Vídeos cadastrados'
    },
    carregarVideos(){ //carregar os videos cadastrados no servidor
      axios.get('http://localhost:8000/videos').then((response) => {
        this.videos = response.data
        this.videosOriginal = response.data
        let i = 0;
        for(i;i < this.videos.length; i++){
          this.videosId.push(this.videos[i].id)
        }
      }) 
    },
    editarVideoMostrar: function(video){
      this.form = 1
      this.videoForm = Object.assign({},video)
      this.tituloSecundario = 'Editar video ' + this.videoForm.id
    },
    cadastrarVideoMostrar(){
      this.form = !this.form
      this.videoForm = Object.assign({},{nome: '', nota: ''})
      this.tituloSecundario = this.tituloSecundario?'':'Cadastrar'
    },
    salvarVideo: function(){
      var video = Object.assign({},this.videoForm)
      if(video.nota < 0 || video.nota > 5 || video.nota == ""){
        video.nota = 0
      }
      if(video.nome.length > 70 || video.nome.length < 1){
        video.nome =  "Video"
      }
      this.form = 0
      this.tituloSecundario = ''
      this.pesquisaNome = ''
      this.pesquisaId = ''
      this.videosId = []
      if(!video.id){ //se não tiver id é para salvar
        let url = 'http://localhost:8000/videos/'
        axios.post(url, video).then((response) =>{
          console.log('Salvo na base de dados! ' + response.data)
          this.carregarVideos()
      })}
      else{ //se tiver o id é para editar
        let url = 'http://localhost:8000/videos/' + video.id
        axios.patch(url,video).then((response) =>{
          console.log('Editado e salvo na base de dados! ' + response.data)
          this.carregarVideos()
      })
    }},
    deletarVideo: function(video){
        var confirmarExclusao = confirm('Deseja realmente excluir o item: ' + video.nome + '?')
        if(confirmarExclusao){
          this.form = 0
          this.tituloSecundario = ''
          this.pesquisaNome = ''
          this.pesquisaId = ''
          this.videosId = []
          var url = 'http://localhost:8000/videos/' + video.id
          axios.delete(url).then((response) =>{
            console.log('Deletado da base de dados! ' + response.data)
            this.carregarVideos()
          })}
    }
  },
  created(){
    this.carregarVideos()
  },
  watch: {
    pesquisaNome: function(valor) {
      this.pesquisaNome = valor
      if(this.pesquisaNome==''){
        this.carregarVideos()
      }
      else{
        var videosPesquisaNome = []
        var i = 0
        for(i; i < this.videosOriginal.length; i++){
          var i2 = 0
          var videoNomeString = '';
          for(i2; i2<this.pesquisaNome.length;i2++){
            videoNomeString += this.videosOriginal[i].nome[i2];
          }
          if(videoNomeString.toLowerCase() == this.pesquisaNome.toLowerCase()){
            videosPesquisaNome.push(this.videosOriginal[i])
          }
        }
        this.videos = videosPesquisaNome
      }
    },
    pesquisaId: function(valor){
      this.pesquisaId = valor
      if(this.pesquisaNome == ''){
        var videoExiste = 0;
        if(this.pesquisaId != ''){
          var i = 0
          for(i; i<this.videos.length;i++){
            if(this.videosId[i] == this.pesquisaId){
              videoExiste = 1
            }
          }
          if(videoExiste){
            var url = 'http://localhost:8000/videos/' + this.pesquisaId
            axios.get(url).then((response) => {
              this.videos = []
              this.videos.push(response.data)
            })
          }
          else{
            this.carregarVideos()
          }
        }
        else{
          this.carregarVideos()
        }
      }
    }
  },
  beforeCreate () {
    document.querySelector('body').setAttribute('style', 'background: linear-gradient(90deg, rgba(0,172,255,0.3) 0%, rgba(0,65,255,0.3) 50%, rgba(16,0,255,0.3) 100%);')
  }
}
</script>

<style scoped>
input {
  margin: 5px;
}
table{
  width:650px;
  table-layout:fixed;
}
th {
  border: 2px solid black;
  background: white;
  font-size: 18px;
  text-align: center;
  min-width: 100px;
}
</style>