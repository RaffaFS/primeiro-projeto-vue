<script setup>
  import { reactive } from 'vue';

  const estado = reactive({
    filtro: 'todas',
    tarefas: [
      {
        titulo: 'Estudar ES6',
        finalizada: false
      },
      {
        titulo: 'Estudar SASS',
        finalizada: false
      },
      {
        titulo: 'Ir para a academia',
        finalizada: true
      }
    ],
    novaTarefa: ''
  })

  function cadastraTarefa(){
    estado.tarefas.push({titulo: estado.novaTarefa, finalizada: false })
    estado.novaTarefa = ''
  }

  const getPendentes = () => {
    return estado.tarefas.filter(tarefa => !tarefa.finalizada)
  }

  const getFinalizadas = () => {
    return estado.tarefas.filter(tarefa => tarefa.finalizada)
  }

  const getfiltradas = () => {
    const filtro = estado.filtro;

    switch (filtro){
      case 'pendentes':
        return getPendentes();
      case 'finalizadas':
        return getFinalizadas();
      default:
        return estado.tarefas
    }
  }

</script>

<template>
  <div class="container">
    <header class="p-5 mb-4 mt-4 bg-light rounded-3">
      <h1>Minhas Tarefas</h1>
      <p>Você possui {{ getPendentes().length }} tarefas pendentes</p>
    </header>
    <form @submit.prevent="cadastraTarefa">
    <div class="row">
      <div class="col">
        <input v-model="estado.novaTarefa" required type="text" placeholder="Descrição da tarefa" class="form-control">
      </div>
      <div class="col-md-2">
        <button type="submit" class="btn btn-primary">Cadastrar</button>
      </div>
      <div class="col-md-2">
        <select @change="elemento => estado.filtro = elemento.target.value" class="form-control">
          <option value="todas">Todas as tarefas</option>
          <option value="pendentes">Tarefas pendentes</option>
          <option value="finalizadas">Tarefas finalizadas</option>
        </select>
      </div>
    </div>
  </form>
  <ul class="list-group mt-4">
    <li class="list-group-item" v-for="tarefa in getfiltradas()">
      <input @change="elemento => tarefa.finalizada = elemento.target.checked" type="checkbox" :checked="tarefa.finalizada" :id="tarefa.titulo">
      <label class="ms-3" :class="{ done: tarefa.finalizada }" :for="tarefa.titulo">{{ tarefa.titulo }}</label>
    </li>
  </ul>
  </div>
</template>

<style scoped>

.done{
  text-decoration: line-through;
}

</style>
