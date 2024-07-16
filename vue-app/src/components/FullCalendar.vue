<template>
  <div class="demo-app flex">
    <edit-event
      v-if="showEdit"
      @close="closeEdit"
    ></edit-event>
    <div class="demo-app-sidebar w-1/4 p-4 bg-gray-100">
      <div class="demo-app-sidebar-section mb-4">
        <h2 class="font-bold text-lg mb-2">All Events ({{ currentEvents.length }})</h2>
        <ul class="list-disc pl-4 mb-4">
          <li v-for="event in currentEvents" :key="event.id" class="mb-2">
            <b>{{ formatDate(event.startStr) }}</b>
            <span>{{ event.title }}</span>
          </li>
        </ul>
        <button class="w-full p-2 bg-red-500 text-white rounded" @click="clearEvents">Clear All Events</button>

        <h2 class="font-bold text-lg mb-2">Bulk Add</h2>
        <div class="mb-2">
          <label for="start" class="block mb-1">Start Date</label>
          <input type="date" id="start" v-model="startDate" name="start" class="w-full p-2 border rounded">
        </div>
        <div class="mb-2">
          <label for="end" class="block mb-1">End Date</label>
          <input type="date" id="end" v-model="endDate" name="end" class="w-full p-2 border rounded">
        </div>
        <div class="mb-2">
          <label for="time" class="block mb-1">Time</label>
          <input type="time" id="time" v-model="time" name="time" class="w-full p-2 border rounded">
        </div>
        <div class="grid grid-cols-2 gap-2 mb-4">
          <div v-for="{ day, value } in weekDaysMap" :key="day" class="flex items-center space-x-2">
            <input type="checkbox" :id="day" :value="value" v-model="selectedWeekDays" class="form-checkbox h-4 w-4 text-blue-600">
            <label :for="day" class="block">{{ day }}</label>
          </div>
        </div>
        <button @click="bulkEventCreate" class="w-full p-2 bg-blue-500 text-white rounded">Add</button>
      </div>
    </div>
    <div class="demo-app-main flex-1 p-4">
      <FullCalendar
        ref="fullCalendar"
        class="demo-app-calendar"
        :options="calendarOptions"
      >
        <template v-slot:eventContent="arg">
          <div class="flex items-center bg-green-500 text-white p-1 rounded">
            <b class="mr-2">{{ formatTime(arg.event.start) }}</b>
            <span class="mr-2">{{ arg.event.title }}</span>
            <i class="close-button cursor-pointer" @click="arg.event.remove()">X</i>
          </div>
        </template>
      </FullCalendar>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import FullCalendar from '@fullcalendar/vue3'
import dayGridPlugin from '@fullcalendar/daygrid'
import timeGridPlugin from '@fullcalendar/timegrid'
import interactionPlugin from '@fullcalendar/interaction'
import EditEvent from './EditEvent.vue'
import { INITIAL_EVENTS, createEventId } from './event-utils'
import moment from 'moment'

const fullCalendar = ref(null)
const showEdit = ref(false)
const startDate = ref('')
const endDate = ref('')
const time = ref('')
const currentEvents = ref([])
const weekDaysMap = [
  { day: 'Sunday', value: 0 },
  { day: 'Monday', value: 1 },
  { day: 'Tuesday', value: 2 },
  { day: 'Wednesday', value: 3 },
  { day: 'Thursday', value: 4 },
  { day: 'Friday', value: 5 },
  { day: 'Saturday', value: 6 }
];
const selectedWeekDays = ref([])

const calendarOptions = reactive({
  plugins: [
    dayGridPlugin,
    timeGridPlugin,
    interactionPlugin
  ],
  headerToolbar: {
    left: 'prev,next today',
    center: 'title',
    right: 'dayGridMonth,timeGridWeek,timeGridDay'
  },
  initialView: 'timeGridWeek',
  initialEvents: INITIAL_EVENTS,
  editable: false,
  selectable: true,
  selectMirror: false,
  dayMaxEvents: true,
  weekends: true,
  select: (selectInfo) => handleDateSelect(selectInfo),
  eventClick: (clickInfo) => handleEventClick(clickInfo),
  eventsSet: (events) => handleEvents(events),
  allDaySlot: false,
  slotEventOverlap: false,
  eventMaxStack: 1,
  slotDuration: '00:15:00',
  slotLabelInterval: '01:00:00',
  slotLabelFormat: {
    hour: 'numeric',
    minute: '2-digit',
    omitZeroMinute: false,
    meridiem: 'short'
  },
  views: {
    timeGridWeek: {
      dayHeaderFormat: (date) => moment(date.date.marker).format('ddd D')
    }
  }
})

const handleDateSelect = (selectInfo) => {
  let title = "";
  let calendarApi = selectInfo.view.calendar

  calendarApi.unselect()
  calendarApi.addEvent({
    id: createEventId(),
    title,
    start: selectInfo.startStr,
    end: moment(selectInfo.startStr).add(15, 'minutes').format(),
    allDay: selectInfo.allDay
  })
}

const handleEventClick = (clickInfo) => {
  showEdit.value = true;
}

const handleEvents = (events) => {
  currentEvents.value = events
}

const formatTime = (date) => {
  return moment(date).format('HH:mm')
}

const formatDate = (date) => {
  return moment(date).format('LLL');
}

const closeEdit = () => {
  showEdit.value = false;
}

const clearEvents = () => {
  fullCalendar.value.getApi().removeAllEvents();
}

const bulkEventCreate = () => {
  const start = startDate.value;
  const end = endDate.value;
  const timeValue = time.value;
  
  if (start && end && timeValue && selectedWeekDays.value.length) {
    let current = moment(start);
    const endMoment = moment(end);
    
    while (current <= endMoment) {
      const currentDay = current.day();
      if (selectedWeekDays.value.includes(currentDay)) {
        const startDateTime = moment(current.format('YYYY-MM-DD') + 'T' + timeValue).format();
        const endDateTime = moment(startDateTime).add(15, 'minutes').format();
        
        fullCalendar.value.getApi().addEvent({
          id: createEventId(),
          title: '',
          start: startDateTime,
          end: endDateTime,
          allDay: false
        });
      }
      current.add(1, 'day');
    }
    startDate.value = '';
    endDate.value = '';
    time.value = '';
    selectedWeekDays.value = [];
  } else {
    alert('Please fill out all fields.');
  }
}
</script>

<style scoped>
.demo-app {
  display: flex;
  height: 100vh;
}
.demo-app-sidebar {
  width: 25%;
  background-color: #f7fafc;
}
.demo-app-main {
  flex: 1;
  padding: 1rem;
}
.close-button:hover {
  color: #f86767;
}
</style>
