<script lang="ts">
  import RequiresPermission from 'src/components/member/RequiresPermission.svelte'
  import TabLink from './TabLink.svelte'

  import {
    eventAttendance,
    eventAttendees,
    eventCarpools,
    eventDetails,
    eventSetlist,
  } from 'src/route/constructors'
  import { EventTab } from 'src/route/types'
  import { editAttendance } from 'src/state/permissions'

  export let eventId: number
  export let currentTab: EventTab | null
</script>

<div class="tabs">
  <ul>
    <TabLink tab={eventDetails} {eventId} {currentTab} />
    <TabLink tab={eventAttendees} {eventId} {currentTab} />
    <TabLink tab={eventSetlist} {eventId} {currentTab} />
    <TabLink tab={eventCarpools} {eventId} {currentTab} />
    <RequiresPermission permission={editAttendance}>
      <TabLink tab={eventAttendance} {eventId} {currentTab} />
    </RequiresPermission>
  </ul>
</div>
