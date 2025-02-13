<script lang="ts">
  import Column from 'src/components/bulma/Column.svelte'
  import Table from 'src/components/bulma/Table.svelte'
  import Button from 'src/components/buttons/Button.svelte'
  import StateBox from 'src/components/remote/StateBox.svelte'

  import { query } from 'src/state/query'
  import { FullMemberQuery } from 'src/gql-operations'
  import {
    emptyLoaded,
    loading,
    RemoteData,
    stateFromResult,
  } from 'src/state/types'
  import { simpleDateWithYearFormatter } from 'src/utils/datetime'

  export let transactions: FullMemberQuery['member']['transactions']
  export let onUpdate: () => void

  let state: RemoteData = emptyLoaded

  async function resolveTransaction(transactionId: number, resolved: boolean) {
    state = loading
    const result = await query('ResolveTransaction', {
      id: transactionId,
      resolved,
    })

    state = stateFromResult(result)
    if (result.type === 'loaded') {
      onUpdate()
    }
  }
</script>

<Column>
  <Table striped>
    <tbody>
      {#each transactions as transaction}
        <tr class="no-bottom-border">
          <td>{simpleDateWithYearFormatter(transaction.time.date)}</td>
          <td>
            {transaction.type}
            {#if transaction.description}
              {` (${transaction.description})`}
            {/if}
          </td>
          <td>
            {#if transaction.amount < 0}
              <span style:color="green">{-1 * transaction.amount}</span>
            {:else}
              {transaction.amount}
            {/if}
          </td>
          <td>{transaction.resolved ? 'Resolved' : 'Outstanding'}</td>
          <td>
            {#if transaction.resolved}
              <Button
                size="is-small"
                click={() => resolveTransaction(transaction.id, false)}
              >
                Unresolve
              </Button>
            {:else}
              <Button
                size="is-small"
                color="is-primary"
                click={() => resolveTransaction(transaction.id, true)}
              >
                Resolve
              </Button>
            {/if}
          </td>
        </tr>
      {/each}
    </tbody>
  </Table>
  <StateBox {state} />
</Column>
