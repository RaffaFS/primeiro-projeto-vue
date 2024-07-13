<script setup>
  // Dando uma descrição inicial ao projeto:
  // A principal ideia aqui é aprender sobre a criação e utilização de componentes
  // Porém, antes precisamos estruturar o projeto sem eles para uma futura comparação
  // Além disso, teremos esse projeto como um caso de estudo mais completo no qual
  // eu vou estar deixando comentários parte a parte

  // RECOMENDO que inicie sempre pela parte HTML do projeto e depois volte para a JS
  // ou no máximo intercale conforme a HTML for chamando funções ou atributos de reactive

  import { reactive } from 'vue';

  const estado = reactive({
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
    filtro: 'todas',
    novaTarefa: ''
  })

  // Começando acima, importamos o reactive e criamos um bloco para ele.
  // O nosso projeto é uma lista de tarefas e cada tarefa possuirá um
  // nome e um algoritmo que nos dirá se ela já foi feita ou não, então
  // crio um atributo que será um array para as tarefas e trato estas
  // como objetos, tendo elas, dois atributos cada: o "título" sendo uma
  // string e o "finalizada" sendo um booleano a ser captado pelo nosso
  // futuro algoritmo

  function cadastraTarefa(){
    estado.tarefas.push({titulo: estado.novaTarefa, finalizada: false })
    estado.novaTarefa = ''
  }

  // A função acima, quando chamada empurra para o array "tarefas", dentro
  // de estado, um novo objeto, com "titulo:" sendo o valor captado no atributo
  // novaTarefa que está atrelado ao valor do input e "finalizada:" sendo um
  // booleano inicial fixo, "false"

  const getPendentes = () => {
    return estado.tarefas.filter(tarefa => !tarefa.finalizada)
  }

  const getFinalizadas = () => {
    return estado.tarefas.filter(tarefa => tarefa.finalizada)
  }

  // Estas duas funções acima são chamadas a partir de uma função mais abaixo,
  // a "getFiltradas". Cada uma delas retorna um "pseudoarray" com os elementos
  // de "estado.tarefas" que passarem pelo filtro

  // Sendo o filtro da primeira o atributo "finalizada" da tarefa ser false
  // e o filtro da segunda o atributo "finalizada" da tarefa ser true

  // Lá no HTML eu farei uma iteração sobre os elementos armazenados no
  // pseudoarray em vigor de acordo com o valor de um <select>

  function defineFiltro(elemento){
    estado.filtro = elemento.target.value
  }

  // Essa acima é uma função chamada a partir da mudança do valor de <select>,
  // ou seja, quando uma nova opção é selecionada, assim sendo ela guarda no
  // argumento o próprio elemento pelo qual foi chamada e em seguida passamos
  // o valor atual desse elemento (<select>), para o atributo filtro em reactive
  // Isso para que "filtro" sempre esteja atualizado quando for chamado na
  // função abaixo

  const getFiltradas = () => {
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

  // getFiltradas capta o valor atual de filtro e de acordo com ele roda um
  // case específico do switch. Cada case chama uma função diferente das que
  // declarei mais acima. Cada uma delas filtra as tarefas à sua própria
  // maneira e nos dá um retorno enquanto o default apenas exibe por completo
  // atributo/array "tarefas"

</script>

<template>

<!-- Vale lembrar que utilizamos o bootstrap na aula para agilizar a construção
    do projeto, então toda tag similar as tags do header abaixo, por exemplo,
    podem ser ignorados no aprendizado do Vue -->

  <div class="container">
    <header class="p-5 mb-4 mt-4 bg-light rounded-3">
      <h1>Minhas Tarefas</h1>
      <p>Você possui {{ getPendentes().length }} tarefas pendentes</p>
    </header>

<!-- Nessa primeira parte, nada de novo, chamamos dentro de {{  }} uma função
    a qual você pode ter mais detalhes sobre, subindo para o script, mas em
    resumo ela retorna um "pseudoarray" e nós utilizamos o comprimento dele
    para a exibição no display -->

    <form @submit.prevent="cadastraTarefa">
      <div class="row">
        <div class="col">
          <input v-model="estado.novaTarefa" required type="text" placeholder="Descrição da tarefa" class="form-control">
        </div>
        <div class="col-md-2">
          <button type="submit" class="btn btn-primary">Cadastrar</button>
        </div>
        <div class="col-md-2">
          <select @change="defineFiltro" class="form-control">
            <option value="todas">Todas as tarefas</option>
            <option value="pendentes">Tarefas pendentes</option>
            <option value="finalizadas">Tarefas finalizadas</option>
          </select>
        </div>
      </div>
    </form>

<!-- Em relação ao formulário acima, podemos notar dois eventos que chamam 
    funções. O primeiro deles sendo um submit do form e o outro sendo um 
    change para o select -->

<!-- Você pode ver mais sobre cada função subindo ao script, o primeiro deles
    é bem óbvio, ele cadastra uma nova tarefa com o mesmo padrão das demais,
    já o segundo utiliza do valor atual do select, de acordo com a <option>
    em vigor e redefine o valor do atributo filtro em reactive que é usado 
    mais adiante em "getFiltradas" -->

<!-- Além disso, em relação ao Vue, temos também um "v-model" no input bem
    no início do form, este atrela o valor do input ao atributo "novaTarefa"
    em estado(reactive), atualizando-o em tempo real, isso é importante
    justamente para o algoritmo de "cadastraTarefa", que faz o uso do valor
    armazenado em "novaTarefa" atrelado ao valor do input -->

    <ul class="list-group mt-4">
      <li class="list-group-item" v-for="tarefa in getFiltradas()">
        <input type="checkbox" :checked="tarefa.finalizada" @change="elemento => tarefa.finalizada = elemento.target.checked" :id="tarefa.titulo">
        <label class="ms-3" :class="{ done: tarefa.finalizada }" :for="tarefa.titulo">{{ tarefa.titulo }}</label>
      </li>
    </ul>

<!-- Aqui, por fim temos a lista de tarefas em si com o sistema de filtros
    onde utilizo no <li> um "v-for" que, para cada "tarefa" em "getFiltradas"
    será renderizado um <li> com a estrutura definida acima utilizando a
    tarefa atual para os campos e valores que vou explicar abaixo -->

<!-- Algo interessante de notar é que nenhuma função é naturalmente iterável
    e essa também não, o que acontece é: a função é chamada uma vez e o "for"
    passa a iterar sobre a constante definida dentro da função logo no início -->

<!-- Para cada tarefa(li) temos uma checkbox e o título da tarefa, temos
    também um "v-bind em checked", que é um campo que deve receber um valor
    booleano e é justamente o que ele recebe, isso é, de acordo com a tarefa
    atual na iteração através de "tarefa.finalizada" -->

<!-- Outro coisa importante de ser citada a parte é que no mesmo checkbox
    temos um evento "change" que dispara uma função anônima, mesmo eu sendo
    um pouco contra o uso de funções anônimas pela leitura do código, isso
    porque essa função anônima se faz necessária assim ser, já que eu faço
    uso do argumento "tarefa" que é definido no <li> e tem o valor alterado
    à cada iteração -->

<!-- O que esse evento faz é dizer que toda tarefa.finalizada que for true 
    deve receber um "checked" na checkbox -->

<!-- Também no mesmo input/checkbox temos um v-bind no id, que recebe o título
    da tarefa atual como id -->

<!-- Falando agora do Label, temos dois "v-bind's", um para class, que
    aplica uma classe caso o atibuto "finalizado" da tarefa na iteração
    atual for true, e outro para for(ancoragem do label), que faz a
    ancoragem com o id definido no input acima durante a mesma iteração
    através de tarefa.titulo -->

  </div>
</template>

<style scoped>

.done{
  text-decoration: line-through;
}

</style>
