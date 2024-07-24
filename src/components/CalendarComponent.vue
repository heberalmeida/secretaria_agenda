<template>
    <div class="calendar-container">
        <CalendarView :show-date="showDate" :items="calendarItems" :itemTop="'1.6em'" :itemBorderHeight="'8px'"
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
                { id: 1, startDate: "2024-07-04", endDate: "2024-07-04", title: "Josuel", classes: ["event-blue"] },
                { id: 2, startDate: "2024-07-05", endDate: "2024-07-07", title: "Congresso UMADECAMP", classes: ["event-orange"] },
                { id: 3, startDate: "2024-07-06", endDate: "2024-07-06", title: "Consagração da UFADECAMP", classes: ["event-write"] },
                { id: 4, startDate: "2024-07-18", endDate: "2024-07-18", title: "Reunião Geral", classes: ["event-blue"] },
                { id: 5, startDate: "2024-07-20", endDate: "2024-07-20", title: "Culto da AADECAAMP", classes: ["event-write"] },
                { id: 6, startDate: "2024-07-23", endDate: "2024-07-23", title: "Reunião Jorge F Setor 11", classes: ["event-write"] },
                { id: 7, startDate: "2024-07-27", endDate: "2024-07-27", title: "Santa Ceia Geral", classes: ["event-gray"] },
                { id: 8, startDate: "2024-07-27", endDate: "2024-07-27", title: "Aniversário de Pastorado", classes: ["event-yellow"] },
                { id: 9, startDate: "2024-08-02", endDate: "2024-08-02", title: "Moacir Frank", classes: ["event-blue"] },
                { id: 10, startDate: "2024-08-03", endDate: "2024-08-03", title: "Consagração da UFADECAMP", classes: ["event-write"] },
                { id: 11, startDate: "2024-08-03", endDate: "2024-08-03", title: "Culto da UMADECAMP", classes: ["event-write"] },
                { id: 12, startDate: "2024-08-16", endDate: "2024-08-18", title: "Congresso UFADECAMP", classes: ["event-orange"] },
                { id: 13, startDate: "2024-08-17", endDate: "2024-08-17", title: "Culto da AADECAAMP", classes: ["event-write"] },
                { id: 14, startDate: "2024-08-19", endDate: "2024-08-20", title: "Setor1 - Crianças e adolescentes", classes: ["event-purple"] },
                { id: 15, startDate: "2024-08-22", endDate: "2024-08-22", title: "CCPO", classes: ["event-green"] },
                { id: 66, startDate: "2024-08-29", endDate: "2024-08-29", title: "CCPO", classes: ["event-green"] },
                { id: 66, startDate: "2024-09-05", endDate: "2024-09-05", title: "CCPO", classes: ["event-green"] },
                { id: 66, startDate: "2024-09-12", endDate: "2024-09-12", title: "CCPO", classes: ["event-green"] },
                { id: 66, startDate: "2024-09-19", endDate: "2024-09-19", title: "CCPO", classes: ["event-green"] },
                { id: 66, startDate: "2024-09-26", endDate: "2024-09-26", title: "CCPO", classes: ["event-green"] },
                { id: 16, startDate: "2024-08-22", endDate: "2024-08-22", title: "Ivanaldo", classes: ["event-blue"] },
                { id: 17, startDate: "2024-08-26", endDate: "2024-08-26", title: "Sandro", classes: ["event-blue"] },
                { id: 18, startDate: "2024-08-29", endDate: "2024-08-29", title: "Leonel", classes: ["event-blue"] },
                { id: 19, startDate: "2024-09-06", endDate: "2024-09-08", title: "Setor4", classes: ["event-purple"] },
                { id: 20, startDate: "2024-09-07", endDate: "2024-09-07", title: "Consagração da UFADECAMP", classes: ["event-write"] },
                { id: 21, startDate: "2024-09-07", endDate: "2024-09-07", title: "Culto da UMADECAMP", classes: ["event-write"] },
                { id: 22, startDate: "2024-09-07", endDate: "2024-09-07", title: "Setor9 - Kids", classes: ["event-purple"] },
                { id: 23, startDate: "2024-09-12", endDate: "2024-09-14", title: "Setor13", classes: ["event-purple"] },
                { id: 25, startDate: "2024-09-12", endDate: "2024-09-15", title: "Setor15", classes: ["event-purple"] },
                { id: 24, startDate: "2024-09-13", endDate: "2024-09-14", title: "Setor9 - Irmãs", classes: ["event-purple"] },
                { id: 25, startDate: "2024-09-13", endDate: "2024-09-15", title: "Setor3 - Jovens e adolescentes", classes: ["event-purple"] },
                { id: 25, startDate: "2024-09-13", endDate: "2024-09-15", title: "Setor6", classes: ["event-purple"] },
                { id: 25, startDate: "2024-09-13", endDate: "2024-09-15", title: "Setor14 - Jovens e Adolesc", classes: ["event-purple"] },
                { id: 25, startDate: "2024-09-13", endDate: "2024-09-15", title: "Setor19", classes: ["event-purple"] },
                { id: 26, startDate: "2024-09-20", endDate: "2024-09-22", title: "Congresso SEMADEMS", classes: ["event-orange"] },
                { id: 27, startDate: "2024-09-21", endDate: "2024-09-21", title: "Culto da AADECAAMP", classes: ["event-write"] },
                { id: 28, startDate: "2024-09-24", endDate: "2024-09-24", title: "Batismo", classes: ["event-verde"] },
                { id: 29, startDate: "2024-09-26", endDate: "2024-09-29", title: "Setor10", classes: ["event-purple"] },
                { id: 29, startDate: "2024-09-27", endDate: "2024-09-28", title: "Setor18", classes: ["event-purple"] },
                { id: 30, startDate: "2024-09-27", endDate: "2024-09-29", title: "Setor17 - Jovens", classes: ["event-purple"] },
                { id: 31, startDate: "2024-10-04", endDate: "2024-10-06", title: "Setor12", classes: ["event-purple"] },
                { id: 32, startDate: "2024-10-04", endDate: "2024-10-06", title: "Setor14 - Irmãs", classes: ["event-purple"] },
                { id: 33, startDate: "2024-10-04", endDate: "2024-10-06", title: "Setor16 - Irmãs", classes: ["event-purple"] },
                { id: 34, startDate: "2024-10-05", endDate: "2024-10-05", title: "Consagração da UFADECAMP", classes: ["event-write"] },
                { id: 35, startDate: "2024-10-05", endDate: "2024-10-05", title: "Culto da UMADECAMP", classes: ["event-write"] },
                { id: 36, startDate: "2024-10-07", endDate: "2024-10-07", title: "Ivaldo", classes: ["event-blue"] },
                { id: 38, startDate: "2024-10-10", endDate: "2024-10-10", title: "Paulo Sergio", classes: ["event-blue"] },
                { id: 39, startDate: "2024-10-12", endDate: "2024-10-12", title: "Congresso ADM Kids", classes: ["event-orange"] },
                { id: 40, startDate: "2024-10-18", endDate: "2024-10-20", title: "Congresso AADECAAMP", classes: ["event-orange"] },
                { id: 41, startDate: "2024-11-15", endDate: "2024-11-17", title: "AGO COMADEMS", classes: ["event-lightblue"] },
                { id: 42, startDate: "2024-10-24", endDate: "2024-10-24", title: "Francemildo", classes: ["event-blue"] },
                { id: 43, startDate: "2024-10-25", endDate: "2024-10-27", title: "Setor1 - Irmãs e Jovens", classes: ["event-purple"] },
                { id: 44, startDate: "2024-10-25", endDate: "2024-10-27", title: "Setor2", classes: ["event-purple"] },
                { id: 45, startDate: "2024-10-25", endDate: "2024-10-27", title: "Setor9 - Jovens e Adolescentes", classes: ["event-purple"] },
                { id: 47, startDate: "2024-11-02", endDate: "2024-11-02", title: "Consagração da UFADECAMP", classes: ["event-orange"] },
                { id: 48, startDate: "2024-11-02", endDate: "2024-11-02", title: "Culto da UMADECAMP", classes: ["event-write"] },
                { id: 49, startDate: "2024-11-07", endDate: "2024-11-10", title: "Setor16 - Unificado", classes: ["event-purple"] },
                { id: 50, startDate: "2024-11-07", endDate: "2024-11-11", title: "Setor5", classes: ["event-purple"] },
                { id: 51, startDate: "2024-11-08", endDate: "2024-11-10", title: "Setor3 - Irmãs", classes: ["event-purple"] },
                { id: 53, startDate: "2024-11-09", endDate: "2024-11-10", title: "Setor20", classes: ["event-purple"] },
                { id: 52, startDate: "2024-11-14", endDate: "2024-11-14", title: "Willian Pereira", classes: ["event-blue"] },
                { id: 54, startDate: "2024-11-16", endDate: "2024-11-16", title: "Culto da AADECAMP", classes: ["event-write"] },
                { id: 54, startDate: "2024-11-16", endDate: "2024-11-16", title: "Setor9 - Missões", classes: ["event-purple"] },
                { id: 54, startDate: "2024-11-22", endDate: "2024-11-24", title: "Setor8", classes: ["event-purple"] },
                { id: 54, startDate: "2024-11-22", endDate: "2024-11-23", title: "Setor20", classes: ["event-purple"] },
                { id: 54, startDate: "2024-11-30", endDate: "2024-11-30", title: "80 Anos", classes: ["event-yellow"] },
                { id: 56, startDate: "2024-12-02", endDate: "2024-12-02", title: "Consagração da UFADECAMP", classes: ["event-write"] },
                { id: 54, startDate: "2024-12-06", endDate: "2024-12-08", title: "Setor11", classes: ["event-purple"] },
                { id: 59, startDate: "2024-12-07", endDate: "2024-12-07", title: "Consagração da UFADECAMP", classes: ["event-write"] },
                { id: 60, startDate: "2024-12-07", endDate: "2024-12-07", title: "Culto da UMADECAMP", classes: ["event-write"] },
                { id: 61, startDate: "2024-12-05", endDate: "2024-12-05", title: "Marcos Aurelio", classes: ["event-blue"] },
                { id: 62, startDate: "2024-12-12", endDate: "2024-12-12", title: "Arciel", classes: ["event-blue"] },
                { id: 63, startDate: "2024-12-17", endDate: "2024-12-17", title: "Batismo", classes: ["event-verde"] },
                { id: 64, startDate: "2024-12-21", endDate: "2024-12-21", title: "Culto Gratidão", classes: ["event-yellow"] },
                { id: 65, startDate: "2024-12-31", endDate: "2024-12-31", title: "Culto da Virada e Santa Ceia Geral", classes: ["event-gray"] }
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
        font-size: 10px !important;
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

    .previousYear, .nextYear {
        display: none;
    }

    .cv-item.span2 {
        max-height: 40px !important;
    }
}
</style>