<script setup>
import { reactive, computed } from "vue";

const estado = reactive({
  filtro: 'soma',
  tarefaTemp: {
    num1: null,
    operator: "",
    num2: null,
    result: 0
  },
  history: [
    {
      num1: 2,
      operator: "+",
      num2: 3,
      result: 5,
      finalizada: false
    },
    {
      num1: 2,
      operator: "-",
      num2: 3,
      result: -1,
      finalizada: true
    },
    {
      num1: 2,
      operator: "x",
      num2: 3,
      result: 6,
      finalizada: false
    },
  ],
  filtroOperacoes: 'todas' 
});

function soma(num1, num2) {
  const result = num1 + num2;
  return {
    num1,
    num2,
    result,
    operator: "+"
  };
}

function sub(num1, num2) {
  const result = num1 - num2;
  return {
    num1,
    num2,
    result,
    operator: "-"
  };
}

function mult(num1, num2) {
  const result = num1 * num2;
  return {
    num1,
    num2,
    result,
    operator: "x"
  };
}

function div(num1, num2) {
  if (num2 === 0) return { num1, num2, result: 'Erro', operator: "/" }; // para evitar divisão por 0
  const result = num1 / num2;
  return {
    num1,
    num2,
    result,
    operator: "/"
  };
}

const getOperacao = () => {
  const { filtro, tarefaTemp } = estado;

  switch (filtro) {
    case 'soma':
      return soma(tarefaTemp.num1, tarefaTemp.num2);
    case 'subtracao':
      return sub(tarefaTemp.num1, tarefaTemp.num2);
    case 'multiplicacao':
      return mult(tarefaTemp.num1, tarefaTemp.num2);
    case 'divisao':
      return div(tarefaTemp.num1, tarefaTemp.num2);
    default:
      return soma(tarefaTemp.num1, tarefaTemp.num2);
  }
};

const atualizarResultado = () => {
  const operacao = getOperacao();
  estado.tarefaTemp.result = operacao.result;
  estado.tarefaTemp.operator = operacao.operator;
};

const cadastraTarefa = () => {
  const tarefaNova = {
    num1: estado.tarefaTemp.num1,
    operator: estado.tarefaTemp.operator,
    num2: estado.tarefaTemp.num2,
    result: estado.tarefaTemp.result,
    finalizada: false
  };

  estado.history.push(tarefaNova);
};

const toggleFinalizada = (index) => {
  estado.history[index].finalizada = !estado.history[index].finalizada;
};

const deletarTarefa = (index) => {
  estado.history.splice(index, 1);
};

const operacoesFiltradas = computed(() => {
  switch (estado.filtroOperacoes) {
    case 'finalizadas':
      return estado.history.filter(tarefa => tarefa.finalizada);
    case 'naoFinalizadas':
      return estado.history.filter(tarefa => !tarefa.finalizada);
    default:
      return estado.history;
  }
});

const quantidadeFiltrada = computed(() => operacoesFiltradas.value.length);

</script>

<template>
  <header class="p-5 mb-4 mt-4 bg-light roudede-3 text-center">
    <h1>Minhas tarefas</h1>
    <p>
      Você possui {{ estado.history.length }} operações salvas
    </p>
  </header>

  <div class="container">
    <form @submit.prevent="cadastraTarefa">
      <div class="row">
        <div class="col-md-4">
          <input v-model.number="estado.tarefaTemp.num1" @keyup="atualizarResultado" required type="number" placeholder="Digite um número" class="form-control">
        </div>
        <div class="col-md-4">
          <input v-model.number="estado.tarefaTemp.num2" @keyup="atualizarResultado" required type="number" placeholder="Digite um número" class="form-control">
        </div>
        <div class="col-md-2">
          <button type="submit" class="btn btn-primary">Salvar</button>
        </div>
        <div class="col-md-2">
          <select v-model="estado.filtro" @change="atualizarResultado" class="form-control">
            <option value="soma">Soma</option>
            <option value="subtracao">Subtração</option>
            <option value="multiplicacao">Multiplicação</option>
            <option value="divisao">Divisão</option>
          </select>
        </div>
      </div>
      <div class="row mt-3 d-flex justify-content-center">
        <div class="col-md-4 text-center">
          Resultado: {{ estado.tarefaTemp.result }}
        </div>
      </div>
    </form>
  
    <div class="row mt-4 d-flex justify-content-center">
      <div class="col-md-4 text-center">
        <select v-model="estado.filtroOperacoes" class="form-control">
          <option value="todas">Todas as operações</option>
          <option value="finalizadas">Operações com line-through</option>
          <option value="naoFinalizadas">Operações sem line-through</option>
        </select>
      </div>
    </div>
    <div class="row mt-3 d-flex justify-content-center">
      <div class="col-md-4 text-center">
        Itens exibidos: {{ quantidadeFiltrada }}
      </div>
    </div>
  
    <ul class="list-group mt-4 d-flex justify-content-center">
      <li 
        class="list-group-item d-flex justify-content-between align-items-center" 
        v-for="(tarefa, index) in operacoesFiltradas" 
        :key="index"
      >
        <span 
          :class="{ 'text-decoration-line-through': tarefa.finalizada }" 
          @click="toggleFinalizada(index)" 
          class="flex-grow-1 text-center"
        >
          {{ tarefa.num1 }} {{ tarefa.operator }} {{ tarefa.num2 }} = {{ tarefa.result }}
        </span>
        <button class="btn btn-danger btn-sm" @click.stop="deletarTarefa(index)">Deletar</button>
      </li>
    </ul>
  </div>
</template>


<style scoped>

.text-decoration-line-through {
  text-decoration: line-through;
}

.container{
  max-width: 1024px;
  /* margin: 0 auto; */
}

</style>
