<script>
  
  import { getContext } from "svelte"	
  import '@fullcalendar/core/locales-all'
  import FullCalendar from './FullCalendarWrapper.svelte'
  import daygridPlugin from '@fullcalendar/daygrid';
  import timeGridPlugin from '@fullcalendar/timegrid';
  import listPlugin from '@fullcalendar/list';
  import {langs, codeLang} from "./lang"
 
  export let language
  export let calendarEvent
  
  export let mappingTitle
  export let mappingDate
  export let mappingStart
  export let mappingEnd
 
  export let mappingTitle2
  export let mappingDate2
  export let mappingStart2
  export let mappingEnd2

  export let dataProvider
  export let dataProvider2

  export let mappingColor
  export let mappingColor2

  export let allday
  export let allday2
  
  export let headerOptionsStart
  export let headerOptionsCenter
  export let headerOptionsEnd

  const DEFAULT_COLOR = '#313131'
  const DEFAULT_COLOR_SECONDARY = '#eb4034'

  const normalizeEvent = (row, titleKey, dateKey, startKey, endKey, color, allDay) => ({
    title: titleKey ? row?.[titleKey] : undefined,
    date: dateKey ? row?.[dateKey] : undefined,
    start: startKey ? row?.[startKey] : undefined,
    end: endKey ? row?.[endKey] : undefined,
    color,
    event: row,
    allDay: allDay ?? false
  })

  const buildEvents = (rows, titleKey, dateKey, startKey, endKey, color, allDay) => {
    if (!Array.isArray(rows)) {
      return []
    }

    return rows.map(row => normalizeEvent(row, titleKey, dateKey, startKey, endKey, color, allDay))
  }

  $: eventsList = [
    ...buildEvents(
      dataProvider?.rows,
      mappingTitle,
      mappingDate,
      mappingStart,
      mappingEnd,
      mappingColor ?? DEFAULT_COLOR,
      allday
    ),
    ...buildEvents(
      dataProvider2?.rows,
      mappingTitle2,
      mappingDate2,
      mappingStart2,
      mappingEnd2,
      mappingColor2 ?? DEFAULT_COLOR_SECONDARY,
      allday2
    )
  ]

  $: localeConfigIndex = codeLang(language)
  $: localeOverrides = localeConfigIndex === null ? {} : langs[localeConfigIndex]

  $: options = {
    headerToolbar: {
      start: headerOptionsStart,
      center: headerOptionsCenter,
      end: headerOptionsEnd
    },
    plugins: [daygridPlugin, listPlugin, timeGridPlugin],
    initialDate: Date.now(),
    locale: language,
    dayMaxEvents: true,
    eventClick: (event)=>{
      calendarEvent?.({
        value: event.event
      })
    },
    events: eventsList,
    eventColor: '#378006',
    theme: true,
    ...localeOverrides
  }
  const { styleable } = getContext("sdk") 
  const component = getContext("component")

</script>

<div use:styleable={$component.styles}>
  <FullCalendar {options} />
</div>
