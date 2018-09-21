<template>
    <div class="container">
        <h1>Vue.js + Github</h1>
        <p class="lead">
            Página que lista issues de um repositório do Github, usando Vue.js.
        </p>
         <div class="row">
            <div class="col">
                <div class="form-group">
                    <input type="text" class="form-control" v-model="username" placeholder="github username">
                </div>
            </div>
             <div class="col">
                <div class="form-group">
                    <input type="text" class="form-control" v-model="repository" placeholder="github repositório">
                </div>
            </div>
             <div class="col-3">
                <div class="form-group">
                    <button @click="getIssues()" class="btn btn-success">GO</button>
                    <button @click="reset()" class="btn btn-danger">LIMPAR</button>
                </div>
            </div>
        </div>
         <br><hr><br>

        <template v-if="selectedIssue.id">
            <h2>{{selectedIssue.title}}</h2>
            <p>{{selectedIssue.body}}</p>
            <button @click="clearIssue()" class="btn  btn-success">voltar</button>
        </template>

         <table v-show="!selectedIssue.id" class="table table-sm table-bordered">
            <thead>
            <tr>
                <th width="100">Número</th>
                <th>Título</th>
            </tr>
            </thead>
             <tbody>
                <tr style="background:#000;" v-if="loader.getIssues || loader.getIssue">
                    <td class="text-center" colspan="2">
                        <img src="/static/audio.svg"/>
                    </td>
                </tr>

                <template v-if="!loader.getIssue">
                    <tr v-if="!!issues.length && !loader.getIssues" 
                    v-for="issue in issues" 
                    :key="issue.number">  <!-- se nao colocar o parametro key no loop vai ficar dando erro, serve pra indentificar cada elemento -->
                        <td><a @click.prevent.stop="getIssue(issue.number)">{{issue.number}}</a></td>
                        <td>{{issue.title}}</td>
                    </tr>
                </template>
                <tr v-if="!!!issues.length && !loader.getIssues"> <!-- os dois primeiros "!!" converte para bool e a terceira "!" serve para negar o resultado -->
                    <td class="text-center" colspan="2">Nenhuma issues encontrada!</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>


<script>
import axios from "axios";

export default {
  name: "GithubIssues",
  methods: {
    reset() {
      this.username = "";
      this.repository = "";
    },
    getIssues() {
      if (this.username == "" || this.repository == "") {
        alert("preencha usuario e repositorio");
        return;
      }

      this.loader.getIssues = true;
      const url = "https://jsonplaceholder.typicode.com/users";
      const urlCurso = `https://api.github.com/repos/${this.username}/${
        this.repository
      }/issues`;
      axios.get(urlCurso).then(result => {
        this.issues = result.data;
      }).finally(()=> this.loader.getIssues = false);
    },
    getIssue(issueId) {
      if (this.username == "" || this.repository == "") {
        alert("preencha usuario e repositorio");
        return;
      }
      this.loader.getIssue = true;
      const urlCurso = `https://api.github.com/repos/${this.username}/${
        this.repository
      }/issues/${issueId}`;
      axios.get(urlCurso).then(response => {
          console.log(response.data);
        this.selectedIssue = response.data;
      }).finally(()=> this.loader.getIssue = false);
    },
    clearIssue(){
        this.selectedIssue ={};
    }
  },
  data() {
    return {
      username: "vuejs",
      repository: "vue",
      issues: [],
      selectedIssue:{},
      loader: {
        getIssues: false,
        getIssue: false
      }
    };
  }
};
</script>