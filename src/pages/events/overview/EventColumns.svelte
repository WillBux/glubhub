<script lang="ts">
  import Columns from 'src/components/bulma/Columns.svelte'
  import EventColumn from './EventColumn.svelte'

  import { LazyRemoteData } from 'src/state/types'
  import { AllEventsQuery } from 'src/gql-operations'

  export let events: LazyRemoteData<AllEventsQuery['events']>
  export let selectedId: number | null
</script>

<Columns>
  <EventColumn
    {events}
    {selectedId}
    title="Volunteer"
    allowedEventTypes={['Volunteer Gig']}
  />
  <EventColumn
    {events}
    {selectedId}
    title="Rehearsal"
    allowedEventTypes={['Rehearsal', 'Sectional']}
  />
  <EventColumn
    {events}
    {selectedId}
    title="Tutti"
    allowedEventTypes={['Tutti Gig']}
  />
  <EventColumn
    {events}
    {selectedId}
    title="Ombuds"
    allowedEventTypes={['Ombuds', 'Other']}
  />
</Columns>
