<template>
  <div class="container mx-auto px-4">
    <h1 class="text-3xl font-bold text-center my-6">Calendário 2025</h1>
    <h2 class="text-3xl font-bold text-center my-6">A COSMOVISÃO BÍBLICA PARA IGREJA CONTEMPORÂNEA </h2>
    
    <!-- Data e horário atual -->
    <div class="text-center text-gray-700 mb-6">
      <p class="text-lg font-semibold">Data e Hora Atual</p>
      <p class="text-xl">{{ formatarData(horarioAtual) }}</p>
    </div>

    <div v-for="mes in eventosPorMes" :key="mes.mes" class="mb-8">
      <h2 class="text-2xl font-semibold text-blue-600 mb-4">{{ mes.mes }}</h2>
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        <div
          v-for="evento in mes.eventos"
          :key="evento.titulo"
          :class="[ 
            'p-4 rounded shadow-md relative',
            eventoEncerrado(evento.dataFim) ? 'bg-gray-200 opacity-50' : getColor(evento.tag)
          ]"
        >
          <!-- Tag (se existir) -->
          <div v-if="evento.tag" class="absolute top-2 right-2 px-2 py-1 rounded text-sm" :class="getColor(evento.tag)">
            {{ evento.tag }}
          </div>

          <!-- Detalhes do evento -->
          <h3 class="text-lg font-bold">{{ evento.titulo }}</h3>
          <p class="text-gray-600">Início: {{ formatarEventoData(evento.dataInicio) }}</p>
          <p class="text-gray-600">Término: {{ formatarEventoData(evento.dataFim) }}</p>

          <!-- Countdown ou mensagem de encerramento -->
          <p v-if="!eventoEncerrado(evento.dataFim) && !eventoIniciado(evento.dataInicio)" class="text-gray-500 text-sm">
            Faltam {{ calcularTempoRestante(evento.dataInicio) }}
          </p>
          <p v-else-if="eventoEncerrado(evento.dataFim)" class="text-red-500 text-sm">Encerrado</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import eventos from "@/data/eventos.json";
import axios from "axios";

// Dados
const eventosPorMes = eventos;
const horarioAtual = ref(null); // Inicialmente nulo

// Função para formatar datas
const formatarData = (data) => {
  const opcoes = {
    weekday: "long",
    year: "numeric",
    month: "long",
    day: "numeric",
    hour: "2-digit",
    minute: "2-digit",
    second: "2-digit",
  };
  return new Date(data).toLocaleDateString("pt-BR", opcoes);
};

// Verificar se o evento está encerrado
const eventoEncerrado = (dataFim) => {
  const agora = new Date(horarioAtual.value);
  const fim = new Date(dataFim);
  return fim < agora;
};

// Verificar se o evento já iniciou
const eventoIniciado = (dataInicio) => {
  const agora = new Date(horarioAtual.value);
  const inicio = new Date(dataInicio);
  return agora >= inicio;
};

// Calcular tempo restante para início
const calcularTempoRestante = (dataInicio) => {
  const agora = new Date(horarioAtual.value);
  const eventoData = new Date(dataInicio);
  const diff = eventoData - agora;

  if (diff <= 0) return null; // Não exibir contagem se já começou

  const dias = Math.floor(diff / (1000 * 60 * 60 * 24));
  const horas = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
  const minutos = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
  const segundos = Math.floor((diff % (1000 * 60)) / 1000);

  return `${dias}d ${horas}h ${minutos}m ${segundos}s`;
};

// Obter cor do evento com base na tag
const getColor = (tag) => {
  switch (tag) {
    case "UFADECAMP":
      return "bg-pink-200 text-pink-700";
    case "UMADECAMP":
      return "bg-green-200 text-green-700";
    case "AADECAMP":
      return "bg-blue-200 text-blue-700";
    case "Batismo":
      return "bg-red-200 text-red-700";
    case "Congresso":
      return "bg-purple-200 text-purple-700";
    case "Geral":
      return "bg-yellow-200 text-yellow-700";
    default:
      return "bg-gray-100 text-gray-700";
  }
};

// Atualizar horário atual com base no backend
const atualizarHorario = async () => {
  try {
    const response = await axios.get("https://api.pokemon.sistemaweb.com.br/date");
    const serverDate = new Date(response.data.date.replace(" ", "T"));
    horarioAtual.value = serverDate;
    setInterval(() => {
      horarioAtual.value = new Date(horarioAtual.value.getTime() + 1000);
    }, 1000);
  } catch (error) {
    console.error("Erro ao obter a data do servidor:", error);
  }
};

// Função para formatar data no formato "segunda, 01/01/2025 às 19:30hs"
const formatarEventoData = (data) => {
  const opcoes = {
    weekday: "long",
    day: "2-digit",
    month: "2-digit",
    year: "numeric",
  };
  const dataFormatada = new Date(data).toLocaleDateString("pt-BR", opcoes);
  const horario = new Date(data).toLocaleTimeString("pt-BR", {
    hour: "2-digit",
    minute: "2-digit",
  });
  return `${dataFormatada} às ${horario.replace(":", "h")}`;
};


// Inicializar a aplicação
onMounted(() => {
  atualizarHorario();
});
</script>

<style>
body {
  font-family: 'Inter', sans-serif;
  background-color: #f9fafb;
}
</style>
