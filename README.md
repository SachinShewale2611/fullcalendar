
# FullCalendar Vue 3 Project

This project implements a calendar using FullCalendar in a Vue 3 application. The calendar includes features like event creation, bulk event addition, event editing, and navigation to specific months and years.

## Features

- **Event Creation**: Add events by selecting a time slot.
- **Event Editing**: Edit event details like title and description.
- **Bulk Event Addition**: Add multiple events over a date range.
- **Month/Year Navigation**: Jump to specific months and years.
- **Clear Events**: Remove all events from the calendar.

## Live Demo

Explore the live demo of this project [here](https://fullcalendar-poc.netlify.app/).

## Project Structure

```
src/
├── components/
│   ├── EditEvent.vue
│   └── FullCalendar.vue
├── event-utils.js
└── App.vue
```

## FullCalendar Configuration

### Plugins

The calendar uses the following FullCalendar plugins:

- `dayGridPlugin`: For month view.
- `timeGridPlugin`: For week and day views.
- `interactionPlugin`: For event interactions (select, drag, etc.).

### Options

```javascript
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
});
```

#### Explanation of Options

- `plugins`: An array of FullCalendar plugins being used.
  - `dayGridPlugin`: Enables the month view.
  - `timeGridPlugin`: Enables the week and day views.
  - `interactionPlugin`: Enables user interactions like selecting and dragging events.

- `headerToolbar`: Configures the calendar header.
  - `left`: Buttons on the left side of the header.
  - `center`: The title of the calendar.
  - `right`: Buttons on the right side of the header.

- `initialView`: The initial view when the calendar loads (e.g., `timeGridWeek`).

- `initialEvents`: An array of initial events to display in the calendar.

- `editable`: If true, allows events to be edited (dragged and resized).

- `selectable`: If true, allows date selection by clicking and dragging.

- `selectMirror`: If true, shows a placeholder event while selecting dates.

- `dayMaxEvents`: Limits the number of events per day and displays a "+more" link if exceeded.

- `weekends`: If true, displays weekends in the calendar.

- `select`: A callback function triggered when a date/time range is selected.

- `eventClick`: A callback function triggered when an event is clicked.

- `eventsSet`: A callback function triggered when the events are set.

- `allDaySlot`: If false, hides the "all-day" slot in timeGrid views.

- `slotEventOverlap`: If false, prevents events from overlapping in timeGrid views.

- `eventMaxStack`: Limits the number of stacked events.

- `slotDuration`: Duration of each slot in the timeGrid views (e.g., `00:30:00` for 30 minutes).

- `slotLabelInterval`: Interval for slot labels in timeGrid views (e.g., `01:00:00` for 1 hour).

- `slotLabelFormat`: Format for slot labels.
  - `hour`: Hour format (e.g., `numeric` for numeric representation).
  - `minute`: Minute format (e.g., `2-digit` for two-digit representation).
  - `omitZeroMinute`: If true, omits the zero minute.
  - `meridiem`: AM/PM format (e.g., `short` for abbreviated representation).

- `views`: Customizes specific views.
  - `timeGridWeek`: Customizes the `timeGridWeek` view.
    - `dayHeaderFormat`: Customizes the day header format using moment.js (e.g., `ddd D` for day and date).

## Event Handling Functions

### `handleDateSelect`

Triggered when a date/time range is selected. Adds a new event to the calendar.

```javascript
const handleDateSelect = (selectInfo) => {
  let title = "";
  let calendarApi = selectInfo.view.calendar;
  calendarApi.unselect();
  calendarApi.addEvent({
    id: createEventId(),
    title,
    start: selectInfo.startStr,
    end: moment(selectInfo.startStr).add(30, 'minutes').format(),
    allDay: selectInfo.allDay
  });
};
```

### `handleEventClick`

Triggered when an event is clicked. Sets the selected event for editing.

```javascript
const handleEventClick = (clickInfo) => {
  const event = clickInfo.event;
  selectedEvent.value = {
    id: event.id,
    title: event.title,
    description: event.extendedProps.description
  };
  showEdit.value = true;
};
```

### `handleEvents`

Triggered when events are set. Updates the current events list.

```javascript
const handleEvents = (events) => {
  currentEvents.value = events;
};
```

### `bulkEventCreate`

Creates multiple events over a specified date range and time, for selected weekdays.

```javascript
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
        const endDateTime = moment(startDateTime).add(30, 'minutes').format();
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
};
```

### `goToMonthYear`

Navigates to a specified month and year.

```javascript
const goToMonthYear = () => {
  const calendarApi = fullCalendar.value.getApi();
  const selectedDate = moment([selectedYear.value, selectedMonth.value]);
  calendarApi.gotoDate(selectedDate.format('YYYY-MM-DD'));
};
```

### `updateEvent`

Updates an existing event with new details.

```javascript
const updateEvent = (updatedEvent) => {
  const calendarApi = fullCalendar.value.getApi();
  let event = calendarApi.getEventById(updatedEvent.id);
  if (event) {
    event.setProp('title', updatedEvent.title);
    event.setExtendedProp('description', updatedEvent.description);
  }
  closeEdit();
};
```

### `printEvents`

Logs all events in the calendar to the console.

```javascript
const printEvents = () => {
  const calendarApi = fullCalendar.value.getApi();
  const events = calendarApi.getEvents();
  events.forEach(event => {
    console.log(`Event ID: ${event.id}, Title: ${event.title}, Start: ${event.start}, End: ${event.end}`);
  });
};
```

### `clearEvents`

Removes all events from the calendar.

```javascript
const clearEvents = () => {
  fullCalendar.value.getApi().removeAllEvents();
};
```
