<script setup lang="ts">
import { ref, onMounted } from 'vue'

const input = ref('')
const result = ref<number | null>(null)
const color = ref('black')
const produtos = ref<string[]>([]) // Lista de produtos
const errorMessage = ref<string | null>(null) // Para exibir erros

const append = (val: string) => {
  input.value += val
}

const clear = () => {
  input.value = ''
  result.value = null
  color.value = 'black'
}

const calculate = () => {
  try {
    result.value = eval(input.value) // Usa eval com cuidado
    color.value = result.value > 100 ? 'red' : 'green'
  } catch {
    result.value = null
    color.value = 'black'
  }
}

// Função para buscar os produtos da API
const fetchProdutos = async () => {
  try {
    const response = await fetch('http://localhost:9090/produtos')
    console.log(response)
    // if (!response.ok) throw new Error('Erro ao buscar produtos')

    produtos.value = await response.json()
    errorMessage.value = null
  } catch (error) {
    errorMessage.value = 'Erro ao conectar com o servidor!'
    produtos.value = []
  }
}

// Chama a API quando o componente for montado
</script>

<template>
  <button @click="fetchProdutos()"></button>
  <div class="calculator">
    <div class="display">
      {{ input }}
      <input v-model="input" readonly />
      <div class="result" :style="{ color }">{{ result !== null ? result : '' }}</div>
    </div>
    <div class="buttons">
      <button @click="append('1')">1</button>
      <button @click="append('2')">2</button>
      <button @click="append('3')">3</button>
      <button @click="append('+')">+</button>

      <button @click="append('4')">4</button>
      <button @click="append('5')">5</button>
      <button @click="append('6')">6</button>
      <button @click="append('-')">-</button>

      <button @click="append('7')">7</button>
      <button @click="append('8')">8</button>
      <button @click="append('9')">9</button>
      <button @click="append('*')">*</button>

      <button @click="clear">C</button>
      <button @click="append('0')">0</button>
      <button @click="calculate">=</button>
      <button @click="append('/')">/</button>
    </div>
  </div>

  <div class="produtos">
    <h3>Lista de Produtos</h3>
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    <ul v-else>
      <li v-for="(produto, index) in produtos" :key="index">{{ produto }}</li>
    </ul>
  </div>
</template>

<style scoped>
.calculator {
  max-width: 220px;
  margin: auto;
  padding: 20px;
  border-radius: 10px;
  background: #f4f4f4;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  text-align: center;
}

.display {
  margin-bottom: 10px;
}

input {
  width: 100%;
  height: 40px;
  text-align: right;
  font-size: 20px;
  padding: 5px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.result {
  font-size: 22px;
  font-weight: bold;
  margin-top: 5px;
}

.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 5px;
}

button {
  padding: 10px;
  font-size: 18px;
  border: none;
  cursor: pointer;
  background: #fff;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

button:hover {
  background: #ddd;
}

.produtos {
  text-align: center;
  margin-top: 20px;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  background: #e3e3e3;
  padding: 10px;
  margin: 5px;
  border-radius: 5px;
}

.error {
  color: red;
  font-weight: bold;
}
</style>
