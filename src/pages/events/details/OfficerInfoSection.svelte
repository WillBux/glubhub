<script lang="ts">
  import Divider from 'src/components/bulma/Divider.svelte'
  import RequiresPermission from 'src/components/member/RequiresPermission.svelte'
  import DeleteModal from 'src/components/popup/DeleteModal.svelte'
  import ContactInfo from './ContactInfo.svelte'

  import { FullEventQuery } from 'src/gql-operations'
  import { eventEdit, routeEvents } from 'src/route/constructors'
  import { viewEventPrivateDetails } from 'src/state/permissions'
  import { query } from 'src/state/query'
  import {
    emptyLoaded,
    loading,
    RemoteData,
    stateFromResult,
  } from 'src/state/types'
  import { replaceRoute } from 'src/store/route'

  export let event: FullEventQuery['event']
  export let onDelete: () => void

  let deleteState: RemoteData | null = null

  async function deleteEvent() {
    deleteState = loading
    const result = await query('DeleteEvent', { id: event.id })

    deleteState = stateFromResult(result)
    if (result.type === 'loaded') {
      onDelete()
    }
  }
</script>

<RequiresPermission permission={viewEventPrivateDetails}>
  <Divider />
  {#if event.gig}
    <ContactInfo gig={event.gig} />
    {#if typeof event.gig.price === 'number'}
      Price: {event.gig.price}
    {/if}
  {/if}
  <br />
  <button
    class="button"
    type="button"
    on:click={() => replaceRoute(routeEvents(event.id, eventEdit))}
    style:margin-bottom="5px"
  >
    Edit this bitch
  </button>
  <br />
  <button
    class="button is-danger is-outlined"
    type="button"
    on:click={() => (deleteState = emptyLoaded)}
    style:margin-bottom="5px"
  >
    Yeet this bitch into the void
  </button>

  {#if deleteState}
    <DeleteModal
      title={`Delete ${event.name}?`}
      cancel={() => (deleteState = null)}
      confirm={deleteEvent}
      state={deleteState}
    >
      <p>
        Are you sure you want to delete this event? Once you delete it, it's
        gone like Donkey Kong.
      </p>
    </DeleteModal>
  {/if}
</RequiresPermission>
