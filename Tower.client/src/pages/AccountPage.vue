<template>
  <div class="about text-center">
    <h4>Welcome {{ account.name }}</h4>
    <img class="rounded-circle" :src="account.picture" alt="" />
    <p>{{ account.email }}</p>
  </div>

  
    <div class="col-12 shadow">
      <div class="row px-5 mb-5 d-flex">
          <div v-for="t in accountTickets" :key="t.id" class="px-5">
              <TowerEventButton :towerEvent="t" />
            <div class="btn btn-outline-danger" @click="remove(t.ticketId)">
              <i class="mdi mdi-delete"></i>
            </div>
          </div>
      </div>
    </div>
</template>

<script>
import { computed, onMounted, watchEffect } from 'vue'
import { AppState } from '../AppState'
import { accountService } from '../services/AccountService'
import { logger } from '../utils/Logger'
import Pop from '../utils/Pop'
import { useRoute } from 'vue-router'
export default {
  name: 'Account',
  props: {
    ticket: {
      type: Object,
      required: true
    }
  },
  setup() {
    const route = useRoute()
    // NOTE on mounted or watch effect, make the api call to your account/tickets and acccount/events
    watchEffect(async () => {
      try {
        await accountService.getAccountTickets()
      } catch (error) {
        logger.log(error)
        Pop.toast(error.message, 'error')
      }
    })
    return {
      async remove(id) {
          try {
              if(await Pop.confirm()) {
                  await accountService.remove(id)
                  Pop.toast("Deleted Ticket")
              }
          } catch (error) {
              logger.error(error)
              Pop.toast(error.message, "error")
          }
      },
      account: computed(() => AppState.account),
      tickets: computed(() => AppState.tickets),
      accountTickets: computed(() => AppState.accountTickets)
    }
  }
}
</script>

<style scoped>
img {
  max-width: 100px;
}
</style>
