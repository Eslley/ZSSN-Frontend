<template>

    <div class="container">

      <h4 class="center-align subtitle-page"><i class="material-icons">view_list</i> Itens</h4>

      <Message :msg="msg" v-show="msg" />

      <Loading :isLoading="isLoading"/>

      <form @submit.prevent="salvar">

        <div class="row">

          <div class="input-field col m10 s12">
            <label for="nome">Nome</label>
            <input id="nome" type="text" placeholder="Nome" v-model="item.nome" >
          </div>

          <div class="input-field col m2 s12">
            <label>Pontos</label>
            <input type="number" placeholder="Pontos" v-model="item.pontos" >
          </div>

        </div>
        
        <div class="center-align">
          <button class="waves-effect waves-light btn-small blue">Salvar<i class="material-icons left">save</i></button>
        </div>
      </form>

      <table class="center">

        <thead>

          <tr>
            <th>Item</th>
            <th>Pontos</th>
            <th class="center-align">Opções</th>
          </tr>

        </thead>

        <tbody>

          <tr v-for="item in itens" :key="item.id">

            <td>{{ item.nome }}</td>
            <td>{{ item.pontos }}</td>
            <td >
                <div class="center-align">
                    <button @click="itemD = item" data-target="modal1" class="waves-effect btn-small red darken-1 modal-trigger"><i class="material-icons">delete_sweep</i></button>
                </div>
            </td>

          </tr>

        </tbody>
      
      </table>

      <div id="modal1" class="modal">
        <div class="modal-content">
          <h4>Confirmar</h4>
          <p>Deseja realmente deletar o item {{itemD.nome}}?</p>
        </div>
        <div class="modal-footer">
          <a @click="deletar()" href="#!" class="modal-close waves-effect waves-green btn-flat">Sim</a>
          <a href="#!" class="modal-close waves-effect waves-green btn-flat">Não</a>
        </div>
      </div>

    </div>

</template>


<script>

  import Itens from '../services/itens';
  import Message from './Message.vue';
  import Loading from './Loading.vue'

  export default {
    name: 'Itens',

    data() {
      return {
        item: {
          nome: '',
          pontos: ''
        },
        itens: [],
        errors: [],
        msg: "",
        itemD: {},
        isLoading: false
      }
    },

    components: {
      Message,
      Loading
    },

    mounted() {
      this.listar()
    },

    methods: {

      showMessage(message) {
        this.msg = message
        setTimeout(() => this.msg = "", 3000)
      },
      
      listar() {
        this.isLoading = true
        Itens.listar().then(response => {
          this.itens = response.data;
          this.isLoading = false
        })
      },

      salvar() {
        this.isLoading = true
        Itens.salvar(this.item).then(response => {

          if(response.status == 201) {
            this.listar()
            this.item = {}
            this.showMessage("Item adicionado!")
          }

          this.isLoading = false
          
        }).catch(err => { 
          this.showMessage("Erro ao salvar")
          this.errors = err.response.data
          this.isLoading = false
        })
      },

      deletar() {
        this.isLoading = true
        Itens.deletar(this.itemD).then(response => {
          if(response.status == 200) {
              this.listar()
              this.showMessage("Item deletado!")
          }    
          
          this.isLoading = false
          
        }).catch(err => {
          this.showMessage("Erro ao deletar")
          this.isLoading = false
        })
        
      }

    },
}
</script>

<style scoped>

  .modal {
    top: 40% !important;
  }

  form button {
    margin: 30px 0 20px 0;
  }

</style>
