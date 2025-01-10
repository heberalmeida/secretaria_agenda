<template>
  <div class="container mx-auto px-4">
    <h1 class="text-3xl font-bold text-center my-6">Calendário 2025</h1>
    <h2 class="text-3xl font-bold text-center my-6">A COSMOVISÃO BÍBLICA PARA IGREJA CONTEMPORÂNEA </h2>
    
    <!-- Data e horário atual -->
    <div class="text-center text-gray-700 mb-6">
      <p class="text-lg font-semibold">Data e Hora Atual</p>
      <p class="text-xl">{{ formatarData(horarioAtual) }}</p>
    </div>

     <!-- Filtro por setor -->
     <div class="mb-6 flex justify-center items-center space-x-4">
      <label for="setor" class="text-lg font-semibold">Mostrar:</label>
      <select
        id="setor"
        v-model="setorSelecionado"
        class="border border-gray-300 rounded px-4 py-2"
      >
        <option value="">Todos os setores</option>
        <option v-for="setor in setores" :key="setor.id" :value="setor.id">
          {{ setor.name }}
        </option>
      </select>
    </div>

    <div v-for="mes in eventosFiltrados" :key="mes.mes" class="mb-8">
      <h2 class="text-2xl font-semibold text-blue-600 mb-4">{{ mes.mes }}</h2>
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        <div
          v-for="(evento, k) in mes.eventos"
          :key="k"
          :class="[ 
            'p-4 rounded shadow-md relative',
            eventoEncerrado(evento.dataFim) ? 'bg-gray-200 opacity-50' : getColor(evento.tag),
            eventoIniciado(evento.dataInicio) && !eventoEncerrado(evento.dataFim) ? 'border-4 border-yellow-500' : ''
          ]"
        >
          <!-- Ribbon para eventos iniciados -->
          <div
            v-if="eventoIniciado(evento.dataInicio) && !eventoEncerrado(evento.dataFim)"
            class="absolute -top-3 left-3 bg-yellow-500 text-white text-xs font-bold px-3 py-1 rounded shadow-md"
          >
            em Andamento
          </div>

          <!-- Tag (se existir) -->
          <div v-if="evento.tag" class="absolute top-2 right-2 px-2 py-1 rounded text-sm" :class="getColor(evento.tag)">
            {{ evento.tag }}
          </div>

          <!-- Detalhes do evento -->
          <h3 class="text-lg font-bold">{{ evento.titulo }}</h3>
          <h3 class="text-lg font-bold" v-html="evento.subtitulo"></h3>

          <!-- Ajuste de Início e Término -->
          <p :class="getTextColor(evento)">
            Início: {{ formatarEventoData(evento.dataInicio) }}
          </p>
          <p :class="getTextColor(evento)">
            Término: {{ formatarEventoData(evento.dataFim) }}
          </p>

          <!-- Ajuste do texto "Faltam" -->
          <p 
            v-if="!eventoEncerrado(evento.dataFim) && !eventoIniciado(evento.dataInicio)" 
            :class="getTextColor(evento)"
            class="text-sm"
          >
            Faltam {{ calcularTempoRestante(evento.dataInicio) }}
          </p>
          <p v-else-if="eventoEncerrado(evento.dataFim)" :class="getTextColor(evento)" class="text-sm">
            Encerrado
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import eventos from "@/data/eventos.json";
import axios from "axios";

// Dados
const eventosPorMes = eventos;
const horarioAtual = ref(null); // Inicialmente nulo
const setorSelecionado = ref(""); // Setor selecionado pelo usuário
const setores = [
  { id: "Sede", name: "Sede" },
  { id: 1, name: "Setor 1 - Aero Rancho" },
  { id: 2, name: "Setor 2 - Guanandi" },
  { id: 3, name: "Setor 3 - Piratininga" },
  { id: 4, name: "Setor 4 - Campo Nobre" },
  { id: 5, name: "Setor 5 - Moreninha" },
  { id: 6, name: "Setor 6 - Tiradentes" },
  { id: 7, name: "Setor 7 - Novo Amazonas" },
  { id: 8, name: "Setor 8 - Itamaracá" },
  { id: 9, name: "Setor 9 - Boa Vista" },
  { id: 10, name: "Setor 10 - Santo Amaro" },
  { id: 11, name: "Setor 11 - Bom Jardim" },
  { id: 12, name: "Setor 12 - Sílvia Regina" },
  { id: 13, name: "Setor 13 - Eldorado" },
  { id: 14, name: "Setor 14 - Nova Lima" },
  { id: 15, name: "Setor 15 - Nova Campo Grande" },
  { id: 16, name: "Setor 16 - Jardim Aero Rancho" },
  { id: 17, name: "Setor 17 - Santo Eugênio" },
  { id: 18, name: "Setor 18 - Rochedinho" },
  { id: 19, name: "Setor 19 - Nogueira" },
  { id: 20, name: "Setor 20 - Santa Emília" },
  { id: "UFADECAMP", name: "UFADECAMP" },
  { id: "UMADECAMP", name: "UMADECAMP" },
  { id: "AADECAMP", name: "AADECAMP" },
  { id: "ADM Kids", name: "ADM Kids" },
];

// Função para formatar datas
const formatarData = (data) => {
  const opcoesData = {
    weekday: "long",
    day: "2-digit",
    month: "long",
    year: "numeric",
  };
  const opcoesHora = {
    hour: "2-digit",
    minute: "2-digit",
    second: "2-digit",
  };
  const dataFormatada = new Date(data).toLocaleDateString("pt-BR", opcoesData);
  const horaFormatada = new Date(data).toLocaleTimeString("pt-BR", opcoesHora);
  return `${dataFormatada} ${horaFormatada}`;
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

const isDarkBackground = (tag) => {
  const darkBackgroundTags = ["Vigília"]; // Tags com fundo escuro
  return darkBackgroundTags.includes(tag);
};

// Função para determinar a cor do texto
const getTextColor = (evento) => {
  if (eventoEncerrado(evento.dataFim)) {
    // Quando o evento está encerrado, sempre usa texto escuro
    return "text-gray-900";
  }
  // Caso contrário, ajusta com base no fundo escuro
  return isDarkBackground(evento.tag) ? "text-white" : "text-gray-600";
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
      return "bg-cyan-200 text-cyan-700"; 
    case "Congresso":
      return "bg-purple-200 text-purple-700";
    case "Geral":
      return "bg-yellow-200 text-yellow-700";
    case "Transição":
      return "bg-orange-200 text-orange-700";
    case "Ensino":
      return "bg-teal-300 text-teal-700";
    case "Aniversário":
      return "bg-indigo-200 text-indigo-700"; // Cor para Aniversário
    case "Vigília":
      return "bg-amber-800 text-amber-100";
    case "Família":
      return "bg-red-200 text-red-700";
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

// Computed para filtrar os eventos com base no setor
const eventosFiltrados = computed(() => {
  if (!setorSelecionado.value) {
    // Retorna todos os eventos se nenhum setor ou departamento estiver selecionado
    return eventosPorMes;
  }

  return eventosPorMes.map((mes) => ({
    mes: mes.mes,
    eventos: mes.eventos.filter((evento) => {
      // Caso seja um departamento (UMADECAMP, UFADECAMP, AADECAMP, etc.)
      if (["UMADECAMP", "UFADECAMP", "AADECAMP", "ADM Kids"].includes(setorSelecionado.value)) {
        // Inclui:
        // 1. Eventos do departamento selecionado (dep corresponde ao selecionado)
        // 2. Eventos que não possuem a chave "setor"
        return (!evento.setor && (!evento.dep || evento.dep.includes(setorSelecionado.value)));
      }

      // Caso seja um setor ou "Sede"
      if (setorSelecionado.value === "Sede") {
        return !evento.setor || evento.setor.includes(0); // Considere '0' como referência para Sede
      }

      // Filtra eventos para o setor selecionado
      return !evento.setor || evento.setor.includes(parseInt(setorSelecionado.value));
    }),
  })).filter((mes) => mes.eventos.length > 0); // Remove meses sem eventos
});

// Inicializar a aplicação
onMounted(() => {
  atualizarHorario();
  // Ordena os eventos por data dentro de cada mês
  eventosPorMes.forEach((mes) => {
    mes.eventos.sort((a, b) => new Date(a.dataInicio) - new Date(b.dataInicio));
  });
});

/*
,
      {
        "titulo": "Pr. Eli Barbosa",
        "subtitulo": "Pr. Jorge Luis Franco",
        "dataInicio": "2025-03-13T19:30:00",
        "dataFim": "2025-03-13T21:00:00",
        "tag": "Aniversário"
      },
      {
        "titulo": "Culto Ensino SEDE",
        "subtitulo": "Setor 1<br>Rita Vieira - Setor 8<br>Moreninha IV - Setor 5",
        "dataInicio": "2025-01-22T19:30:00",
        "dataFim": "2025-01-22T21:00:00",
        "tag": "Ensino"
      }
*/
</script>

<style>
body {
  font-family: 'Inter', sans-serif;
  background-color: #f9fafb;
}

.border-4 {
  border-width: 4px;
}
.border-yellow-500 {
  border-color: #eab308; /* Amarelo */
}

.shadow-md {
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.-top-3 {
  top: -0.75rem; /* Ajusta a posição vertical */
}

.left-3 {
  left: 0.75rem; /* Ajusta a posição horizontal */
}

</style>
