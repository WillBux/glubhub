<script lang="ts">
  import Box from 'src/components/bulma/Box.svelte'
  import Column from 'src/components/bulma/Column.svelte'
  import Columns from 'src/components/bulma/Columns.svelte'
  import Container from 'src/components/bulma/Container.svelte'
  import Section from 'src/components/bulma/Section.svelte'
  import Remote from 'src/components/remote/Remote.svelte'
  import UserDetailColumn from './UserDetailColumn.svelte'
  import UserActions from './UserActions.svelte'
  import Attendance from './Attendance.svelte'
  import Semesters from './Semesters.svelte'
  import Details from './Details.svelte'
  import Money from './Money.svelte'

  import {
    profileAttendance,
    profileDetails,
    profileMoney,
    profileSemesters,
    routeProfile,
  } from 'src/route/constructors'
  import { ProfileTab } from 'src/route/types'
  import { eagerQuery } from 'src/state/query'
  import { siteContext } from 'src/store/context'
  import { renderRoute } from 'src/route/render'

  export let email: string
  export let tab: ProfileTab | null

  $: [member, reloadMember] = eagerQuery('FullMember', { email })

  const allProfileTabs = [
    profileDetails,
    profileMoney,
    profileAttendance,
    profileSemesters,
  ]
</script>

<Section>
  <Container>
    <Remote data={$member}>
      <Columns slot="loaded" let:data={member}>
        <Column narrow>
          <!-- Profile Picture -->
          <img
            width={250}
            height={250}
            src={member.member.picture || 'https://picsum.photos/250'}
            alt=""
          />
        </Column>
        <Column>
          <UserDetailColumn member={member.member} />
          <br />
          <UserActions member={member.member} />
        </Column>
      </Columns>
    </Remote>
  </Container>
</Section>

{#if $siteContext.user?.positions.length}
  <Section>
    <Container>
      <Box>
        <Remote data={$member}>
          <svelte:fragment slot="loaded" let:data={member}>
            <div class="tabs">
              <ul>
                {#each allProfileTabs as profileTab}
                  <li class:is-active={tab?.route === profileTab.route}>
                    <a
                      href={renderRoute(
                        routeProfile(member.member.email, profileTab)
                      )}
                    >
                      {profileTab.name}
                    </a>
                  </li>
                {/each}
              </ul>
            </div>

            {#if tab?.route === 'attendance'}
              <Attendance
                member={member.member}
                onUpdate={() => reloadMember({ email })}
              />
            {:else if tab?.route === 'semesters'}
              <Semesters email={member.member.email} />
            {:else if tab?.route === 'money'}
              <Money
                transactions={member.member.transactions}
                onUpdate={() => reloadMember({ email })}
              />
            {:else if tab?.route === 'details'}
              <Details
                member={member.member}
                onUpdate={() => reloadMember({ email })}
              />
            {/if}
          </svelte:fragment>
        </Remote>
      </Box>
    </Container>
  </Section>
{/if}
