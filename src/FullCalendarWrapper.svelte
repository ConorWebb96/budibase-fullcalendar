<script>
  import { onMount } from 'svelte'
  import { Calendar } from '@fullcalendar/core'
  import '@fullcalendar/core/preact.js'

  let classes = null
  export { classes as class }
  export let style = null
  export let options

  export function getAPI() {
    return calendar
  }

  let calendarEl
  let calendar

  onMount(() => {
    if (!canBeInitiated) {
      return
    }

    initCalendar()

    return () => {
      if (calendar) {
        calendar.destroy()
        calendar = undefined
      }
    }
  })

  $: hasPlugins = options && options.plugins && options.plugins.length
  $: canBeInitiated = !!(options && hasPlugins && calendarEl && !calendar)

  $: if (calendar && hasPlugins) {
    updateCalendarOptions()
  }

  $: if (canBeInitiated) {
    initCalendar()
  }

  function initCalendar() {
    calendar = new Calendar(calendarEl, options)
    calendar.render()
  }

  function updateCalendarOptions() {
    calendar.pauseRendering()
    calendar.resetOptions(options)
    calendar.resumeRendering()
  }
</script>

<div bind:this={calendarEl} class={classes} {style}></div>
