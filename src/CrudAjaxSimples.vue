<template lang="html">
<div >
  <br>
  <div v-if="carregando" class="text-center">
    <i class="fa fa-spinner fa-pulse fa-2x fa-fw"></i>
    <br>Carregando...<br><br>
  </div>
  <div v-else>
        <!-- Alert  -->
        <div class="row" v-show="alertBox.show">
          <div class="col-md-2 sm-2"></div>
          <div class="col-md-8 sm-8">
            <div class="alert alert-success">
              <a href="#" class="close" data-dismiss="alert">&times;</a>
              <i class="fa fa-check-circle" aria-hidden="true"></i>
              {{ alertBox.msg }}
            </div>
          </div>
        </div>
        <!--Cadastrar e Editar -->
        <div v-show="idUpdate" class="row">
              <h4 class="text-center text-success">
                <span v-if="idUpdate == 'novo'">Cadastrar </span>
                <span v-else>Editar </span>
                 {{ name }}
              </h4>

              <div  v-for="(fField, index) in formFields" v-show="fField.form">
                  <hr>
                  <div class="row" >
                      <div class="col-md-1"></div>
                      <div class="col-md-2 col-sm-2">
                        <label :for="index">{{ fField.label }}</label>
                      </div>
                      <div class="col-md-6 sm-8">
                        <div :class="form.errors.has(index) | inputClassErro">

                        <input class="form-control" type="text" v-model="form[index]"
                              :placeholder="fField.label" v-show="fField.type == 'text'">

                        <textarea rows="3" class="form-control"
                                  v-model="form[index]" :placeholder="fField.label"
                                  v-show="fField.type == 'textarea'">
                                </textarea>

                        <span class="help-block" v-if="form.errors.has(index)">
                          <has-error :form="form" :field="index"/>
                        </span>
                      </div>
                    </div>
                  </div>
            </div>
            <div class="row">
              <div class="col-md-12">
                <hr>
              </div>
            </div>
            <div class="col-md-12">
                  <center>
                    <button type="button" class="btn btn-default" @click="limpar">
                      <i class="fa fa-reply" aria-hidden="true"></i>
                      Voltar
                    </button>
                    <button type="button" class="btn btn-primary" v-if="idUpdate == 'novo'" @click="create">
                      <i class="fa fa-plus-square-o" aria-hidden="true"></i>
                      Cadastrar
                    </button>
                    <button type="button" class="btn btn-primary"  v-else @click="update">
                      <i class="fa fa-floppy-o" aria-hidden="true"></i>
                      Salvar
                    </button>
                    <br><br>
                  </center>
            </div>

        </div>
        <!-- Congirmação -->
        <div class="row" v-show="idDelete">
          <div class="col-md-12">
            <center>
              Deseja realmente Excluir? <br>
              <button type="button" class="btn btn-default" @click="limpar">
                <i class="fa fa-ban" aria-hidden="true"></i>
                Não
              </button>
              <button type="button" class="btn btn-danger" @click="deleted">
                <i class="fa fa-check-circle" aria-hidden="true"></i>
                Sim
              </button>
              <br><br>
            </center>
          </div>
        </div>
        <!-- pesquisa e adição -->
        <div class="row" v-show="!idUpdate && !idDelete">
            <div class="col-md-6">
                <button type="button"
                        class="btn btn-success"
                        @click="novo"
                        v-show="urlPost">
                  <i class="fa fa-plus" aria-hidden="true"></i>
                    Novo
                </button>
            </div>
            <div class="col-md-6">

                  <div class="form-group has-feedback">
                          <input v-model="filter" placeholder="Pesquisar..."
                                class="form-control" @input="editarInputBusca"
                                data-toggle="tooltip" data-placement="top"
                                title="Digite o nome para pesquisar">
                          <span class="form-control-feedback">
                            <span class="fa fa-search"></span>
                          </span>
                    </div>
            </div>
        </div>
  </div>
  <!--  -->
  <div v-if="!idUpdate">

    <b-table striped hover :items="items" :fields="fields"
          :current-page="currentPage" :per-page="perPage"
          :filter="filter">

          <template v-for="(field, index) in fields" :slot="index" slot-scope="item">
            <span :class="classTextTable(item.item.id)" v-if="item.item[index]">
              {{ item.item[index] | truncate(80) }}
            </span>
          </template>
          <!-- btn ações  -->
          <template slot="acoes" slot-scope="item" >
            <!--  -->
            <button type="button" :class="classBtnTable(item.item.id)"
            @click="setUpdate(item.item)"
            v-show="urlPut">
            <i class="fa fa-pencil-square-o" aria-hidden="true"></i>
          </button>
          <!--  -->
          <button type="button" :class="classBtnTable(item.item.id)"
          @click="setDelete(item.item.id)"
          v-show="urlDelete">
          <i class="fa fa-trash" aria-hidden="true"></i>
        </button>
      </template>
      </b-table>

      <center v-show="mostrarPaginacao" class="paginacao">
        <pagination size="md" :total-rows="this.items.length"
        :per-page="perPage" v-model="currentPage"/>
      </center>
  </div>

</div>
</template>
<script>

import Form from 'vform'
import { HasError } from 'vform'
import axios from 'axios'
import { mapValues, clone } from 'lodash'

export default {

  name: 'crud-ajax-simples',

  components:{
    HasError
  },

  props: {
    name: '',
    fields: {},
    urlGetAll: '',
    urlPost: '',
    urlPut: '',
    urlDelete: '',
    maxTextTable : 100
  },

  data(){
    return{
      carregando: false,
      idDelete : null,
      idUpdate : null,
      form : {},
      formFields : {},
      currentPage: 1,
      opsOfPage : [{text:10,value:10},{text:15,value:15},{text:20,value:20}],
      items: [],
      perPage: 10,
      filter: null,
      alertBox : { }
    }
  },

  methods:{

      alert(msg){
          this.alertBox = { show : true, msg : msg }
          setTimeout( () => {
            this.alertBox = { show : false }
          }, 2000)
      },

      editarInputBusca(){
          this.currentPage = 1
      },

      // fechar ação
      limpar(){
          this.idDelete = null
          this.idUpdate = null
      },

      //buscar registros
      getAll(){
          this.carregando = true
          axios.get(this.urlGetAll).then(({data})=>{
              this.limpar()
              this.items = data
              this.carregando = false
              // console.log(data);
          }).catch((e)=>{
              this.carregando = false
              console.log(e);
          })
      },

      // atualizar retistro
      update(){

          if (this.idUpdate) {
              this.carregando = true
              this.form.put(this.urlPut + "/" + this.idUpdate).then(({ data }) => {
                this.alert('Salvo com Sucesso!')
                this.limpar()
                this.getAll()
                this.carregando = false
                // console.log(data)
              }).catch((e)=>{
                  this.carregando = false
                  console.log(e);
              })
          }
      },

      // setar item no formulário para ser editado
      setUpdate(item){
          this.limpar()
          this.idUpdate = item.id
          this.form.keys().forEach(key => {
            this.form[key] = item[key]
          })
          // console.log(this.form);
      },

      //btn novo limpa o formulário
      novo(){
          this.limpar()
          this.idUpdate = 'novo'
          let values = mapValues(this.formFields, (o) => { return '' })
          this.form = new Form(values)
      },

      //cadastrar novo registro
      create(){
          this.carregando = true
          this.form.post(this.urlPost).then(({ data }) => {
            this.getAll()
            this.alert('Cadastrado com Sucesso!')
            this.limpar()
            this.carregando = false
            // console.log(data)
          }).catch((e)=>{
              this.carregando = false
              console.log(e);
          })
      },

      // setar item a ser deletado
      setDelete(id){
          this.limpar()
          this.idDelete = id
      },

      //deletar na api
      deleted(){
          if (this.idDelete) {
              this.carregando = true
              this.form.delete(this.urlDelete + '/' + this.idDelete).then(({ data }) => {
                this.alert('Deletado com Sucesso!')
                this.limpar()
                this.getAll()
                this.carregando = false
                // console.log(data)
              }).catch((e)=>{
                  this.carregando = false
                  console.log(e);
              })
          }
      },

      classTextTable(id){
         switch (id) {
           case this.idUpdate:
             return 'text-success item-selected-update'
             break;
           case this.idDelete:
             return 'text-danger item-selected-delete'
             break;
           default:
             return ''
         }
      },

      classBtnTable(id){
         switch (id) {
           case this.idUpdate:
             return 'btn btn-default btn-xs btn-border-update'
             break;
           case this.idDelete:
             return 'btn btn-default btn-xs btn-border-delete'
             break;
           default:
             return 'btn btn-default btn-xs'
         }
      }

  },

  computed:{
       mostrarPaginacao(){
            return this.items.length > 10 ? true : false
       }
  },

  created(){
      this.getAll()
      this.formFields = clone(this.fields)
      let values = mapValues(this.formFields, (o) => { return '' })
      this.form = new Form(values)
  }

}

</script>

<style lang="css">

.item-selected-delete{
  text-decoration: line-through;
}

.item-selected-update{
  text-decoration: underline;
  font-weight: bold;
}

.btn-border-delete{
  border-color: rgb(203, 1, 31);
  color: rgb(203, 1, 31);
}
.btn-border-delete:hover{
  border-color: rgb(203, 1, 31);
  color: rgb(203, 1, 31);
}

.btn-border-update{
  border-color: rgb(1, 203, 33);
  color: rgb(1, 203, 33);
}
.btn-border-update:hover{
  border-color: rgb(1, 203, 33);
  color: rgb(1, 203, 33);
}

</style>
