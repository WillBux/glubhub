<script lang="ts">
  import Table from 'src/components/bulma/Table.svelte'
  import GigRequestButtons from './GigRequestButtons.svelte'
  import SingleGigRequest from './SingleGigRequest.svelte'

  import { AllGigRequestsQuery } from 'src/gql-operations'

  export let gigRequests: AllGigRequestsQuery['gigRequests']
  export let reopen: (id: number) => void
  export let dismiss: (id: number) => void
</script>

<Table scrollable fullwidth>
  <thead>
    <tr style:width="100%">
      <th>When Submitted</th>
      <th>Event Name</th>
      <th>Event Date</th>
      <th>Contact</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    {#if gigRequests.length > 0}
      {#each gigRequests as gigRequest}
        <SingleGigRequest {gigRequest} />
        <GigRequestButtons {gigRequest} {reopen} {dismiss} />
      {/each}
    {:else}
      <br />
      <tr style="text-align: center">
        <td colspan="5">
          No gig requests here, <i>officer</i>.
        </td>
      </tr>
    {/if}
  </tbody>
</Table>
