<template>
  <app-list
    v-if="auth.isSubscriber && user.pins"
    :items="pinned"
    class="pb-0 mb-n2"
    nav
  >
    <template #item="{ props: itemProps }">
      <v-hover>
        <template #default="{ props: activatorProps, isHovering }">
          <v-list-item
            :title="itemProps.title"
            :to="itemProps.to"
            class="mb-1"
            v-bind="activatorProps"
            @click.prevent="onClickPin(itemProps.to)"
          >
            <template #append>
              <v-icon
                v-if="isHovering"
                icon="mdi-pin-off"
                size="16"
                class="me-1"
                @click.prevent.stop="onClickPinRemove(itemProps)"
              />
            </template>
          </v-list-item>
        </template>
      </v-hover>
    </template>
  </app-list>
</template>

<script setup>
  // Composables
  import { useRouter } from 'vue-router'

  // Utilities
  import { computed, onBeforeMount } from 'vue'

  // Stores
  import { useAuthStore, useUserStore } from '@vuetify/one'
  import { usePinsStore } from '@/store/pins'

  const auth = useAuthStore()
  const pins = usePinsStore()
  const user = useUserStore()
  const router = useRouter()

  const pinned = computed(() => ([{
    activeIcon: 'mdi-pin',
    inactiveIcon: 'mdi-pin-outline',
    items: [...pins.pins],
    title: 'Pinned',
  }]))

  async function onClickPin (to) {
    pins.isPinning = true

    await router.replace(to)

    pins.isPinning = false
  }

  function onClickPinRemove (pin) {
    pins.toggle(false, pin)
  }

  onBeforeMount(() => {
    pins.load()
  })
</script>
