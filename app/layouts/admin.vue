<template>
  <div
    class="grid grid-cols-1 md:grid-cols-[250px_minmax(0,1fr)] lg:grid-cols-[300px_minmax(0,1fr)]"
  >
    <aside class="h-dvh hidden md:block sticky top-0 z-20 bg-background">
      <UiScrollArea class="h-dvh border-r py-5 px-3">
        <div class="font-semibold text-lg py-5">Welcome {{ user.username }}</div>
        <UiDivider />
        <nav class="mt-6 flex flex-col">
          <UiButton
            exact-active-class="bg-secondary"
            variant="ghost"
            class="justify-start gap-4"
            to="/admin/contacts"
            ><Icon class="text-muted-foreground size-5" name="lucide:users" />
            Contacts</UiButton
          >
        </nav>
      </UiScrollArea>
    </aside>
    <main>
      <header class="z-20 border-b">
        <UiContainer class="flex gap-3 h-[68px] items-center justify-end">
          <UiButton
            @click="$colorMode.preference = $colorMode.value == 'dark' ? 'light' : 'dark'"
            size="icon-sm"
            variant="outline"
          >
            <Icon name="lucide:sun" size="18" />
          </UiButton>
          <UiButton @click="logout()" size="icon-sm" variant="outline">
            <Icon name="lucide:log-out" size="18" />
          </UiButton>
        </UiContainer>
      </header>
      <UiContainer class="py-5">
        <slot />
      </UiContainer>
    </main>
  </div>
</template>

<script lang="ts" setup>
const { user } = storeToRefs(useAuthStore());
const logout = () => {
  useAuthStore().logout();
  useRouter().go(0);
};
</script>

<style></style>
