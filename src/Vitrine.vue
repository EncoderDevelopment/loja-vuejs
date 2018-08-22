<template>
<body>

    <div class="container" style="border:none; margin-top:-80px;">      

        <div class="col-lg-12" style="border:none;">
            <div class="row" >
                <div v-for="produto in produtos" style="
    float: left;
    width: 200px; height: 200px;
    margin: 70px 10px; border:none;
">
                    <div class="col-lg-2 col-md-4 mb-2">
                        <div class="card h-100">
                           <a href="#">
                                <img class="card-img-top" src="{{produto.photo}}" alt="" style="width: 100%;" />
                            </a>
                            
                             <div class="card-body">
                                <h4 class="card-title">
                                    <a href="#">{{produto.name}}</a>
                                </h4>
                                <h5>{{produto.price}}</h5>
                                <p class="card-text">{{produto.descript}}</p>
                            </div>
                            <div class="card-footer">
                                <small class="text-muted">&#9733; &#9733; &#9733; &#9733; &#9734;</small>
                            </div>
                        </div>
                    </div>
               </div>
            </div>
        </div>
    </div>
    <!-- Footer -->
    <footer class="py-5 bg-dark">
        <div class="container">

        </div>
        <!-- /.container -->
    </footer>   

</body>
</template>

<script>
  import Pagination from './Pagination.vue'
  

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
        showModal:false
      }
    },
    components: {
      Pagination
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