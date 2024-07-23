<template>
    <div class="calendar-container">
        <CalendarView :show-date="showDate" :items="calendarItems"
            class="theme-default holiday-us-traditional holiday-us-official" :wrap-item-title-on-hover="true"
            :do-emit-item-mouse-events="true" @item-mouseenter="showTooltip" @item-mouseleave="hideTooltip">
            <template #header="headerProps">
                <CalendarViewHeader v-bind="headerProps" @input="setShowDate" />
            </template>
            <template #item="{ value, weekStartDate, top }">
                <div :class="['cv-item', ...value.classes]" :style="{ top }" @mouseover="showTooltip(value, $event)"
                    @mouseout="hideTooltip">
                    <span v-if="value.startTime" class="startTime">{{ value.startTime }}</span>
                    <span v-if="value.endTime" class="endTime">{{ value.endTime }}</span>
                    <span>{{ value.title }}</span>
                </div>
            </template>
        </CalendarView>
        <div v-if="tooltipVisible" :style="tooltipStyle" class="tooltip">
            {{ tooltipContent }}
        </div>
    </div>
</template>

<script>
import { CalendarView, CalendarViewHeader } from "vue-simple-calendar";
import "vue-simple-calendar/dist/style.css";
import "vue-simple-calendar/dist/css/default.css";
import "vue-simple-calendar/dist/css/holidays-us.css";

export default {
    name: "CalendarComponent",
    data() {
        return {
            showDate: new Date(),
            tooltipVisible: false,
            tooltipContent: '',
            tooltipStyle: {},
            calendarItems: [
                { id: 1, startDate: "2024-07-04", endDate: "2024-07-04", title: "Josuel", tooltip: "Josuel", classes: ["event-blue"] },
                { id: 2, startDate: "2024-07-05", endDate: "2024-07-07", title: "Congresso UMADECAMP", tooltip: "Congresso UMADECAMP", classes: ["event-orange"] },
                { id: 3, startDate: "2024-07-06", endDate: "2024-07-06", title: "Consagração da UFADECAMP", tooltip: "Consagração da UFADECAMP", classes: ["event-white"] },
                { id: 4, startDate: "2024-07-18", endDate: "2024-07-18", title: "Reunião Geral", tooltip: "Reunião Geral", classes: ["event-blue"] },
                { id: 5, startDate: "2024-07-20", endDate: "2024-07-20", title: "Culto da AADECAAMP", tooltip: "Culto da AADECAAMP", classes: ["event-white"] },
                { id: 6, startDate: "2024-07-23", endDate: "2024-07-23", title: "Reunião Jorge F Setor 11", tooltip: "Reunião Jorge F Setor 11", classes: ["event-white"] },
                { id: 7, startDate: "2024-07-27", endDate: "2024-07-27", title: "Santa Ceia Geral", tooltip: "Santa Ceia Geral", classes: ["event-gray"] },
                { id: 8, startDate: "2024-07-27", endDate: "2024-07-27", title: "Aniversário de Pastorado", tooltip: "Aniversário de Pastorado", classes: ["event-yellow"] },
                { id: 9, startDate: "2024-08-02", endDate: "2024-08-02", title: "Moacir Frank", tooltip: "Moacir Frank", classes: ["event-blue"] },
                { id: 10, startDate: "2024-08-03", endDate: "2024-08-03", title: "Consagração da UFADECAMP", tooltip: "Consagração da UFADECAMP", classes: ["event-white"] },
                { id: 11, startDate: "2024-08-03", endDate: "2024-08-03", title: "Culto da UMADECAMP", tooltip: "Culto da UMADECAMP", classes: ["event-white"] },
                { id: 12, startDate: "2024-08-16", endDate: "2024-08-18", title: "Congresso UFADECAMP", tooltip: "Congresso UFADECAMP", classes: ["event-orange"] },
                { id: 13, startDate: "2024-08-17", endDate: "2024-08-17", title: "Culto da AADECAAMP", tooltip: "Culto da AADECAAMP", classes: ["event-white"] },
                { id: 14, startDate: "2024-08-19", endDate: "2024-08-20", title: "Setor 1 - Crianças e adolescentes", tooltip: "Setor 1 - Crianças e adolescentes", classes: ["event-purple"] },
                { id: 15, startDate: "2024-08-22", endDate: "2024-09-26", title: "CCPO", tooltip: "CCPO", classes: ["event-green"], repeat: "weekly", dayOfWeek: 4 },
                { id: 16, startDate: "2024-08-22", endDate: "2024-08-22", title: "Ivanaldo", tooltip: "Ivanaldo", classes: ["event-blue"] },
                { id: 17, startDate: "2024-08-26", endDate: "2024-08-26", title: "Sandro", tooltip: "Sandro", classes: ["event-blue"] },
                { id: 18, startDate: "2024-08-29", endDate: "2024-08-29", title: "Leonel", tooltip: "Leonel", classes: ["event-blue"] },
                { id: 19, startDate: "2024-09-06", endDate: "2024-09-08", title: "Setor 4", tooltip: "Setor 4", classes: ["event-purple"] },
                { id: 20, startDate: "2024-09-07", endDate: "2024-09-07", title: "Consagração da UFADECAMP", tooltip: "Consagração da UFADECAMP", classes: ["event-white"] },
                { id: 21, startDate: "2024-09-07", endDate: "2024-09-07", title: "Culto da UMADECAMP", tooltip: "Culto da UMADECAMP", classes: ["event-white"] },
                { id: 22, startDate: "2024-09-07", endDate: "2024-09-07", title: "Setor 9 - Kids", tooltip: "Setor 9 - Kids", classes: ["event-purple"] },
                { id: 23, startDate: "2024-09-12", endDate: "2024-09-14", title: "Setor 13", tooltip: "Setor 13", classes: ["event-purple"] },
                { id: 24, startDate: "2024-09-13", endDate: "2024-09-15", title: "Setor 3 - Jovens e adolescentes", tooltip: "Setor 3 - Jovens e adolescentes", classes: ["event-purple"] },
                { id: 25, startDate: "2024-09-13", endDate: "2024-09-15", title: "Setor 9 - Irmãs", tooltip: "Setor 9 - Irmãs", classes: ["event-purple"] },
                { id: 26, startDate: "2024-09-13", endDate: "2024-09-15", title: "Setor 6", tooltip: "Setor 6", classes: ["event-purple"] },
                { id: 27, startDate: "2024-09-13", endDate: "2024-09-15", title: "Setor 14 - Jovens e Adolesc", tooltip: "Setor 14 - Jovens e Adolesc", classes: ["event-purple"] },
                { id: 28, startDate: "2024-09-13", endDate: "2024-09-15", title: "Setor 19", tooltip: "Setor 19", classes: ["event-purple"] },
                { id: 29, startDate: "2024-09-20", endDate: "2024-09-22", title: "Congresso SEMADEMS", tooltip: "Congresso SEMADEMS", classes: ["event-orange"] },
                { id: 30, startDate: "2024-09-21", endDate: "2024-09-21", title: "Culto da AADECAAMP", tooltip: "Culto da AADECAAMP", classes: ["event-white"] },
                { id: 31, startDate: "2024-09-24", endDate: "2024-09-24", title: "Batismo", tooltip: "Batismo", classes: ["event-green"] },
                { id: 32, startDate: "2024-09-26", endDate: "2024-09-29", title: "Setor 10", tooltip: "Setor 10", classes: ["event-purple"] },
                { id: 33, startDate: "2024-09-27", endDate: "2024-09-28", title: "Setor 18", tooltip: "Setor 18", classes: ["event-purple"] },
                { id: 34, startDate: "2024-09-27", endDate: "2024-09-29", title: "Setor 17 - Jovens", tooltip: "Setor 17 - Jovens", classes: ["event-purple"] },
                { id: 35, startDate: "2024-10-04", endDate: "2024-10-06", title: "Setor 12", tooltip: "Setor 12", classes: ["event-purple"] },
                { id: 36, startDate: "2024-10-04", endDate: "2024-10-06", title: "Setor 14 - Irmãs", tooltip: "Setor 14 - Irmãs", classes: ["event-purple"] },
                { id: 37, startDate: "2024-10-04", endDate: "2024-10-06", title: "Setor 16 - Irmãs", tooltip: "Setor 16 - Irmãs", classes: ["event-purple"] },
                { id: 38, startDate: "2024-10-05", endDate: "2024-10-05", title: "Consagração da UFADECAMP", tooltip: "Consagração da UFADECAMP", classes: ["event-white"] },
                { id: 39, startDate: "2024-10-05", endDate: "2024-10-05", title: "Culto da UMADECAMP", tooltip: "Culto da UMADECAMP", classes: ["event-white"] },
                { id: 40, startDate: "2024-10-07", endDate: "2024-10-07", title: "Ivaldo", tooltip: "Ivaldo", classes: ["event-blue"] },
                { id: 41, startDate: "2024-10-10", endDate: "2024-10-10", title: "Paulo Sergio", tooltip: "Paulo Sergio", classes: ["event-blue"] },
                { id: 42, startDate: "2024-10-12", endDate: "2024-10-12", title: "Congresso ADM Kids", tooltip: "Congresso ADM Kids", classes: ["event-orange"] },
                { id: 43, startDate: "2024-10-18", endDate: "2024-10-20", title: "Congresso AADECAAMP", tooltip: "Congresso AADECAAMP", classes: ["event-orange"] },
                { id: 44, startDate: "2024-11-15", endDate: "2024-11-17", title: "AGO COMADEMS", tooltip: "AGO COMADEMS", classes: ["event-lightblue"] },
                { id: 45, startDate: "2024-10-24", endDate: "2024-10-24", title: "Francemildo", tooltip: "Francemildo", classes: ["event-blue"] },
                { id: 46, startDate: "2024-10-25", endDate: "2024-10-27", title: "Setor 1 - Irmãs e Jovens", tooltip: "Setor 1 - Irmãs e Jovens", classes: ["event-purple"] },
                { id: 47, startDate: "2024-10-25", endDate: "2024-10-27", title: "Setor 2", tooltip: "Setor 2", classes: ["event-purple"] },
                { id: 48, startDate: "2024-10-25", endDate: "2024-10-27", title: "Setor 9 - Jovens e Adolescentes", tooltip: "Setor 9 - Jovens e Adolescentes", classes: ["event-purple"] },
                { id: 49, startDate: "2024-11-02", endDate: "2024-11-02", title: "Consagração da UFADECAMP", tooltip: "Consagração da UFADECAMP", classes: ["event-orange"] },
                { id: 50, startDate: "2024-11-02", endDate: "2024-11-02", title: "Culto da UMADECAMP", tooltip: "Culto da UMADECAMP", classes: ["event-white"] },
                { id: 51, startDate: "2024-11-07", endDate: "2024-11-10", title: "Setor 16 - Unificado", tooltip: "Setor 16 - Unificado", classes: ["event-purple"] },
                { id: 52, startDate: "2024-11-07", endDate: "2024-11-11", title: "Setor 5", tooltip: "Setor 5", classes: ["event-purple"] },
                { id: 53, startDate: "2024-11-08", endDate: "2024-11-10", title: "Setor 3 - Irmãs", tooltip: "Setor 3 - Irmãs", classes: ["event-purple"] },
                { id: 54, startDate: "2024-11-09", endDate: "2024-11-10", title: "Setor 20", tooltip: "Setor 20", classes: ["event-purple"] },
                { id: 55, startDate: "2024-11-14", endDate: "2024-11-14", title: "Willian Pereira", tooltip: "Willian Pereira", classes: ["event-blue"] },
                { id: 56, startDate: "2024-11-16", endDate: "2024-11-16", title: "Culto da AADECAMP", tooltip: "Culto da AADECAMP", classes: ["event-white"] },
                { id: 57, startDate: "2024-11-16", endDate: "2024-11-16", title: "Setor 9 - Missões", tooltip: "Setor 9 - Missões", classes: ["event-purple"] },
                { id: 58, startDate: "2024-11-22", endDate: "2024-11-24", title: "Setor 8", tooltip: "Setor 8", classes: ["event-purple"] },
                { id: 59, startDate: "2024-11-22", endDate: "2024-11-23", title: "Setor 20", tooltip: "Setor 20", classes: ["event-purple"] },
                { id: 60, startDate: "2024-11-30", endDate: "2024-11-30", title: "80 Anos", tooltip: "80 Anos", classes: ["event-yellow"] },
                { id: 61, startDate: "2024-12-02", endDate: "2024-12-02", title: "Consagração da UFADECAMP", tooltip: "Consagração da UFADECAMP", classes: ["event-white"] },
                { id: 62, startDate: "2024-12-06", endDate: "2024-12-08", title: "Setor 11", tooltip: "Setor 11", classes: ["event-purple"] },
                { id: 63, startDate: "2024-12-07", endDate: "2024-12-07", title: "Consagração da UFADECAMP", tooltip: "Consagração da UFADECAMP", classes: ["event-white"] },
                { id: 64, startDate: "2024-12-07", endDate: "2024-12-07", title: "Culto da UMADECAMP", tooltip: "Culto da UMADECAMP", classes: ["event-white"] },
                { id: 65, startDate: "2024-12-05", endDate: "2024-12-05", title: "Marcos Aurelio", tooltip: "Marcos Aurelio", classes: ["event-blue"] },
                { id: 66, startDate: "2024-12-12", endDate: "2024-12-12", title: "Arciel", tooltip: "Arciel", classes: ["event-blue"] },
                { id: 67, startDate: "2024-12-17", endDate: "2024-12-17", title: "Batismo", tooltip: "Batismo", classes: ["event-green"] },
                { id: 68, startDate: "2024-12-21", endDate: "2024-12-21", title: "Culto Gratidão", tooltip: "Culto Gratidão", classes: ["event-yellow"] },
                { id: 69, startDate: "2024-12-31", endDate: "2024-12-31", title: "Culto da Virada e Santa Ceia Geral", tooltip: "Culto da Virada e Santa Ceia Geral", classes: ["event-gray"] }
            ]
        };
    },
    components: {
        CalendarView,
        CalendarViewHeader,
    },
    methods: {
        setShowDate(d) {
            this.showDate = d;
        },
        showTooltip(item, event) {
            const tooltipWidth = 150; // Largura máxima do tooltip
            const tooltipHeight = 50; // Altura máxima do tooltip
            const viewportWidth = window.innerWidth;
            const viewportHeight = window.innerHeight;
            let top = event.clientY + 5;
            let left = event.clientX + 5;

            if (left + tooltipWidth > viewportWidth) {
                left = viewportWidth - tooltipWidth - 10;
            }

            if (top + tooltipHeight > viewportHeight) {
                top = viewportHeight - tooltipHeight - 10;
            }

            this.tooltipContent = item.title;
            this.tooltipStyle = {
                top: `${top}px`,
                left: `${left}px`,
                position: 'absolute',
                maxWidth: `${tooltipWidth}px`,
                maxHeight: `${tooltipHeight}px`,
            };
            this.tooltipVisible = true;
        },
        hideTooltip() {
            this.tooltipVisible = false;
        }
    }
};
</script>

<style>
.calendar-container {
    width: 90vw;
    height: 80vh;
    display: flex;
    justify-content: center;
    align-items: center;
}

.tooltip {
    background-color: rgba(0, 0, 0, 0.75);
    color: white;
    padding: 5px;
    border-radius: 3px;
    pointer-events: none;
    z-index: 10;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

.event-white {
    background-color: #fff !important;
    color: black !important;
}

.event-blue {
    background-color: #007bff !important;
    color: white !important;
}

.event-orange {
    background-color: #ff9800 !important;
    color: white !important;
}

.event-purple {
    background-color: #9c27b0 !important;
    color: white !important;
}

.event-yellow {
    background-color: #ffeb3b !important;
    color: black !important;
}

.event-green {
    background-color: #4caf50 !important;
    color: white !important;
}

.event-gray {
    background-color: #9e9e9e !important;
    color: white !important;
}

.event-lightblue {
    background-color: #03a9f4 !important;
    color: white !important;
}

.event-verde {
    background-color: #00ff63 !important;
    color: black !important;
}

@media (max-width: 600px) {
    .cv-item {
        font-size: 12px !important;
    }

    .theme-default .cv-header .periodLabel {
        font-size: 1em !important;
    }

    .theme-default .cv-item {
        text-overflow: initial;
        text-wrap: wrap;
        display: block;
    }

    .cv-header button {
        font-size: 12px;
    }

    .tooltip {
        font-size: 10px;
    }
}
</style>