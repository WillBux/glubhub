<script lang="ts">
  import LinkButton from 'src/components/buttons/LinkButton.svelte'

  import { adminMoney, routeAdmin } from 'src/route/constructors'
</script>

<LinkButton className="is-pulled-right" route={routeAdmin(adminMoney(null))}>
  ABORT! ABORT!
</LinkButton>
