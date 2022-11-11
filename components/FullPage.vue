<template>
  <div
    class="
      absolute
      w-screen
      h-screen
      backdrop-filter backdrop-filter backdrop-blur-sm
      z-10
      content-center
      flex
      align-center
      justify-center
    "
  >
    <section class="m-auto">
      <div class="card">
        <slot />
      </div>
    </section>
  </div>
</template>

<script>
export default {};
</script>

<style>
.card {
  @apply border-2 border-gray-900 bg-gray-50 p-3 m-2 rounded-xl;
}
</style>
