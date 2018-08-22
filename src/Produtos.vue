<template>
  <a class="fixo button is-large is-danger is-loading" v-show="isLoading">Loading</a>
  <div class="container">
    <h1 class="title">{{title}}</h1>
    <div class="columns">
      <div class="column is-5">
        <p class="control has-addons">
          <input class="input is-expanded" type="text" placeholder="Procure pelo nome" v-model="search">
          <a class="button is-info" @click.prevent="searchProdutos">Search</a>
        </p>
      </div>
      <div class="column is-2">         
      </div>
      <div class="column is-2">         
      </div>
      <div class="column is-4">

      <div class="btn-group" role="group" aria-label="Basic example">   
        <button type="button" class="btn btn-secondary button is-info" @click.prevent="vitrineProdutos">Vitrine de produtos</button>
        <button type="button" class="btn btn-secondary button is-info" @click.prevent="newProdutos">Novo produto</button>       
      </div>
</div>
       



    </div>
    <div class="columns">
      <div class="column is-12">
        <table class="table is-narrow is-bordered">
          <thead>
            <th>Nome</th>
            <th>Foto</th>
            <th>Descrição</th>
            <th>Preço</th>
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="produto in produtos">
            <td>{{produto.name}}</td>
            <td><img src="{{produto.photo}}" style="width: 100pX;"></td>
            <td>{{produto.descript}}</td>
            <td>{{produto.price}}</td> 

            <td class="is-icon">
              <a href="#" @click.prevent="editProdutos(produto)">
                <i class="fa fa-edit"></i>
              </a>
              <a href="#" @click.prevent="removeProduto(produto)">
                <i class="fa fa-trash"></i>
              </a>
            </td>
          </tr>
        </tbody>
      </table>
      <Pagination :total="total" :page="page" :itens-per-page="itensPerPage" @change-page="onChangePage"></Pagination>
    </div>
  </div>
</div>



<div id="modal_vitrine" class="modal" :class="{'is-active':showModalVitrine}" style="width:100% !important; height: 100%!important; background-color: white;">
  <div class="modal-background" style="width:100% !important; background-color: white; height: 100%!important; "></div>
  <div class="modal-card" style="width:100% !important; background-color: white; height: 100%!important; ">
    <header class="modal-card-head">
      <p class="modal-card-title">Vitrine</p>
      <button class="delete" @click.prevent="showModalVitrine=false"></button>
    </header>
    <section class="modal-card-body">
      <Vitrine></vitrine>
    </section>
    <footer class="modal-card-foot">
      
    </footer>
  </div>
</div>


<div id="modal_produto" class="modal" :class="{'is-active':showModal}">
  <div class="modal-background"></div>
  <div class="modal-card">
    <header class="modal-card-head">
      <p class="modal-card-title">Produto: {{selected.name}}</p>
      <button class="delete" @click.prevent="showModal=false"></button>
    </header>
    <section class="modal-card-body">


    <div class="columns">
      <div class="column">
        <label class="label">Nome</label>
          <p class="control">
            <input class="input" type="text" placeholder="Nome" v-model="selected.name" required="true">
          </p>
      </div>
      <div class="column">
         <label class="label">Preço</label>
    <p class="control">
      <input class="input" type="text" placeholder="Preço" v-model="selected.price" id="price" name="price" required="true">
    </p>
      </div>
      </div>

      <label class="label">Descrição</label>
      <p class="control">
        <textarea class="textarea" placeholder="Descrição" v-model="selected.descript" required="true"></textarea>
      </p>
      
   <label class="label">URL da foto</label>
    <p class="control">
      <input class="input" type="text" placeholder="URL" v-model="selected.photo" required="true">
    </p>

    </section>
    <footer class="modal-card-foot">
      <a class="button is-primary" @click.prevent="saveProduto">Salvar</a>
      <a class="button" @click.prevent="showModal=false">Cancelar</a>
    </footer>
  </div>
</div>
</template>

<script>
  import Pagination from './Pagination.vue';
  import Vitrine from './Vitrine.vue';

  export default {

    data () {
      return {
        isLoading: false,
        title: 'Loja Virtual',
        search: '',
        produtos: [],
        page: 1,
        total: 0,
        selected: {},
        itensPerPage: 10,
        showModal:false,
        showModalVitrine:false
      }
    },
    components: {
      Pagination,
      Vitrine
    },


    methods: {
      onChangePage(page){
        this.page = page
        this.loadProdutos()
      },
      showLoading(){
        this.isLoading=true;
      },
      hideLoading(){
        this.isLoading=false;
      },
      loadProdutos(){

        let t = this
        this.showLoading()

        let start = (this.page * this.itensPerPage) - (this.itensPerPage)
        let end = this.page * this.itensPerPage
        let qString = "";

        if (this.search){
          qString = `&q=${this.search}`
        }

        this.$http.get(`/produtos?_start=${start}&_end=${end}${qString}`).then(
         response=>{
           t.produtos = response.json()
           t.total = response.headers['X-Total-Count']
         },
         error=>{
           console.log(error)
         }).finally(function () {
          t.hideLoading();
        })

       },
       searchProdutos(){
        this.loadProdutos()
       },
       newProdutos(){
        this.selected={}
        this.showModal = true;
       },
       editProdutos(produto){
        this.selected=produto
        this.showModal = true;
       },
       vitrineProdutos(){      
       this.loadProdutos();  
        this.showModalVitrine = true;
       },
       removeProduto(produto){
        let self = this;
        swal({  title: "Você tem certeza?",
                 text: `Deseja deletar "${produto.name}"`,   
                 type: "warning",   
                 showCancelButton: true,   
                 confirmButtonColor: "#DD6B55",   
                 cancelButtonText: "Cancelar",
                 confirmButtonText: "Sim, pode apagar!", 
                 showLoaderOnConfirm: true,  
                 closeOnConfirm: false }, function(){   
                  
                  self.$http.delete(`/produtos/${produto.id}`).then(
                    result=>{
                      swal("Produto deletado!")
                      self.loadProdutos()
                    })
        });

       },
       saveProduto(){
        if (this.selected.id!=null){  //EDIT
          this.$http.put(`/produtos/${this.selected.id}`,this.selected).then(
            response=>{
              this.$set('selected',{})
              this.$set('showModal',false)
            },
            error=>{
              console.error(error)
            }
            ).finally(
              this.loadProdutos()
            )
          }
          else
          { //NEW
            this.$http.post(`/produtos`,this.selected).then(
            response=>{
              this.$set('selected',{})
              this.$set('showModal',false)
            },
            error=>{
              console.error(error)
            }
            ).finally(
              this.loadProdutos()
            )
          }
       }
     },
     created(){
      this.loadProdutos();
    },
  }

  

</script>
<style>
  .fixo{float: right; margin-right: 10px; margin-top: 0px; z-index: 1000;}
</style>