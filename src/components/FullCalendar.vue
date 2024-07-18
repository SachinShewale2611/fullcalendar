<template>
  <div class="demo-app flex">
    <!-- Edit Event Modal start-->
    <EditEvent
      v-if="showEdit"
      @close="closeEdit"
      :selected-event="selectedEvent"
      @update="updateEvent"
    ></EditEvent>
    
    <!-- Sidebar -->
    <div class="demo-app-sidebar w-1/4 p-4 bg-gray-100">
      <div class="demo-app-sidebar-section mb-4">

        <!-- Print Events Button -->
        <div class="border-2 rounded-md mt-4 p-2">
          <button @click="printEvents" class="w-full p-2 bg-blue-500 text-white rounded">Print Events</button>

          <!-- Go to Month/Year Section  -->
          <h2 class="font-bold text-lg mb-2">Go to Month/Year</h2>
          <div class="mb-2">
            <label for="month" class="block mb-1">Month</label>
            <select id="month" v-model="selectedMonth" class="w-full p-2 border rounded">
              <option v-for="(month, index) in months" :key="index" :value="index">{{ month }}</option>
            </select>
          </div>
          <div class="mb-2">
            <label for="year" class="block mb-1">Year</label>
            <input type="number" id="year" v-model="selectedYear" class="w-full p-2 border rounded">
          </div>
          <button @click="goToMonthYear" class="w-full p-2 bg-blue-500 text-white rounded">Go</button>
        </div>

        <!-- Bulk Add Events Section -->
        <div class="border-2 rounded-md mt-4 p-2">
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
            <label
            for="time" class="block mb-1">Time</label>
            <input type="time" id="time" v-model="time" name="time" class="w-full p-2 border rounded">
          </div>

          <!-- Weekday Selection for Bulk Add -->
          <div class="grid grid-cols-2 gap-2 mb-4">
            <div v-for="{ day, value } in weekDaysMap" :key="day" class="flex items-center space-x-2">
              <input type="checkbox" :id="day" :value="value" v-model="selectedWeekDays" class="form-checkbox h-4 w-4 text-blue-600">
              <label :for="day" class="block">{{ day }}</label>
            </div>
          </div>
          <button @click="bulkEventCreate" class="w-full p-2 bg-blue-500 text-white rounded">Add</button>
        </div>

        <!-- All Events List -->
        <div class="border-2 rounded-md mt-4 p-2">
          <h2 class="font-bold text-lg mb-2">All Events ({{ currentEvents.length }})</h2>
          <ul class="list-disc pl-4 mb-4">
            <li v-for="event in currentEvents" :key="event.id" class="mb-2">
              <b>{{ formatDate(event.startStr) }}</b>
              <br>
              <b>- ID: {{ event.id }}</b>
              <br>
              <span>Title: {{ event.title }}</span>
              <br>
              <span>Description: {{ event.extendedProps.description }}</span>
            </li>
          </ul>
          <button class="w-full p-2 bg-red-500 text-white rounded" @click="clearEvents">Clear All Events</button>
        </div>
      </div>
    </div>

    <!-- Main Calendar -->
    <div class="demo-app-main flex-1 p-4">
      <FullCalendar
        ref="fullCalendar"
        class="demo-app-calendar"
        :options="calendarOptions"
      >
        <template v-slot:eventContent='arg'>
          <b>{{ formatTime(arg.event.start) }}</b>
          <span @click="editEvent(arg.event)" class="ml-2 cursor-pointer">Edit</span>
          <span class="close-button relative float-right cursor-pointer mr-2"
            @click="arg.event.remove()">X</span>
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

// References and Reactive State
const fullCalendar = ref(null)
const showEdit = ref(false)
const startDate = ref('')
const endDate = ref('')
const time = ref('')
const currentEvents = ref([])
const selectedWeekDays = ref([])

// Weekdays Map for Bulk Add
const weekDaysMap = [
  { day: 'Sunday', value: 0 },
  { day: 'Monday', value: 1 },
  { day: 'Tuesday', value: 2 },
  { day: 'Wednesday', value: 3 },
  { day: 'Thursday', value: 4 },
  { day: 'Friday', value: 5 },
  { day: 'Saturday', value: 6 }
]

// Month Names for Dropdown
const months = [
  'January', 'February', 'March', 'April', 'May', 'June',
  'July', 'August', 'September', 'October', 'November', 'December'
]
const selectedMonth = ref(new Date().getMonth())
const selectedYear = ref(new Date().getFullYear())

// FullCalendar Options
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
  slotDuration: '00:30:00',
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

// Selected Event for Editing
const selectedEvent = ref(null)

const handleDateSelect = (selectInfo) => {
  let title = ""
  let calendarApi = selectInfo.view.calendar
  calendarApi.unselect()
  calendarApi.addEvent({
    id: createEventId(),
    title,
    start: selectInfo.startStr,
    end: moment(selectInfo.startStr).add(30, 'minutes').format(),
    allDay: selectInfo.allDay
  })
}

const handleEventClick = (clickInfo) => {
  const event = clickInfo.event;
  selectedEvent.value = {
    id: event.id,
    title: event.title,
    description: event.extendedProps.description
  };
  showEdit.value = true;
}

const editEvent = (event) => {
  selectedEvent.value = {
    id: event.id,
    title: event.title,
    description: event.extendedProps.description
  };
  showEdit.value = true;
}

const handleEvents = (events) => {
  currentEvents.value = events
}

const formatTime = (date) => {
  return moment(date).format('HH:mm')
}

const formatDate = (date) => {
  return moment(date).format('LLL')
}

const closeEdit = () => {
  showEdit.value = false
}

const clearEvents = () => {
  fullCalendar.value.getApi().removeAllEvents()
}

const bulkEventCreate = () => {
  const start = startDate.value
  const end = endDate.value
  const timeValue = time.value
  if (start && end && timeValue && selectedWeekDays.value.length) {
    let current = moment(start)
    const endMoment = moment(end)
    while (current <= endMoment) {
      const currentDay = current.day()
      if (selectedWeekDays.value.includes(currentDay)) {
        const startDateTime = moment(current.format('YYYY-MM-DD') + 'T' + timeValue).format()
        const endDateTime = moment(startDateTime).add(30, 'minutes').format()
        fullCalendar.value.getApi().addEvent({
          id: createEventId(),
          title: '',
          start: startDateTime,
          end: endDateTime,
          allDay: false
        })
      }
      current.add(1, 'day')
    }
    startDate.value = ''
    endDate.value = ''
    time.value = ''
    selectedWeekDays.value = []
  } else {
    alert('Please fill out all fields.')
  }
}

const goToMonthYear = () => {
  const calendarApi = fullCalendar.value.getApi()
  const selectedDate = moment([selectedYear.value, selectedMonth.value])
  calendarApi.gotoDate(selectedDate.format('YYYY-MM-DD'))
}

const updateEvent = (updatedEvent) => {
  const calendarApi = fullCalendar.value.getApi();
  let event = calendarApi.getEventById(updatedEvent.id);
  if (event) {
    event.setProp('title', updatedEvent.title);
    event.setExtendedProp('description', updatedEvent.description);
  }
  closeEdit();
};

const printEvents = () => {
  const calendarApi = fullCalendar.value.getApi();
  const events = calendarApi.getEvents();
  events.forEach(event => {
    console.log(`Event ID: ${event.id}, Title: ${event.title}, Start: ${event.start}, End: ${event.end}`);
  })
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
