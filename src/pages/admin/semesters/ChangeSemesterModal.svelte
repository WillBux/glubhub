<script lang="ts">
  import Column from 'src/components/bulma/Column.svelte'
  import Button from 'src/components/buttons/Button.svelte'
  import SelectInput from 'src/components/forms/SelectInput.svelte'
  import Modal from 'src/components/popup/Modal.svelte'
  import ErrorBox from 'src/components/remote/ErrorBox.svelte'

  import { adminSemesters, routeAdmin } from 'src/route/constructors'
  import { semesterType } from 'src/state/input'
  import { eagerQuery, query } from 'src/state/query'
  import {
    emptyLoaded,
    loading,
    RemoteData,
    stateFromResult,
  } from 'src/state/types'
  import { siteContext } from 'src/store/context'
  import { replaceRoute } from 'src/store/route'
  import { derived, get } from 'svelte/store'

  let selected = get(siteContext).currentSemester.name
  let state: RemoteData = emptyLoaded

  const [allSemesters, _reloadAllSemesters] = eagerQuery('AllSemesters', {})
  const loadedSemesters = derived(
    [siteContext, allSemesters],
    ([context, ss]) =>
      ss.type === 'loaded'
        ? ss.data.semesters.map((semester) => semester.name)
        : [context.currentSemester.name]
  )

  function closeModal() {
    replaceRoute(routeAdmin(adminSemesters(null)))
  }

  async function changeSemester() {
    if (!selected || selected === get(siteContext).currentSemester.name) return

    state = loading
    const result = await query('SetCurrentSemester', { name: selected })

    state = stateFromResult(result)
    if (result.type === 'loaded') {
      closeModal()
      window.location.reload()
    }
  }
</script>

<Modal close={closeModal}>
  <Column>
    <h2 class="subtitle is-2">Which semester do you want to switch to?</h2>
    <p>
      This will change everything. You really only want to do this at the
      beginning of a new semester. If it's not a solstice, then don't.
    </p>

    <br />
    <div class="field is-grouped is-grouped-centered">
      <SelectInput
        loading={$allSemesters.type === 'loading'}
        type={semesterType($loadedSemesters)}
        values={$loadedSemesters}
        {selected}
        onInput={(newSelected) => {
          if (newSelected) {
            selected = newSelected
          }
        }}
      />
    </div>
    <br />
    <Button
      element="a"
      color="is-primary"
      className="is-pulled-left"
      loading={state.type === 'loading'}
      click={() => changeSemester()}
    >
      The ol' Glub Hub switcharoo
    </Button>
    <Button element="a" className="is-pulled-right" click={closeModal}>
      ABORT! ABORT!
    </Button>
    <br />
    {#if state.type === 'error'}
      <ErrorBox error={state.error} />
    {/if}
  </Column>
</Modal>
