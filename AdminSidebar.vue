<template>
  <aside class="w-64 bg-[#ede1f1] h-fullscreen p-6 space-y-4">
    <h2 class="text-2xl font-bold text-[#26103d]">Admin</h2>
    <nav class="flex flex-col gap-4 text-[#26103d]">
      <NuxtLink to="/admin/dashboard">Dashboard</NuxtLink>
      <NuxtLink to="/admin/order">Manage Orders</NuxtLink>
      <NuxtLink to="/admin/profile">Profile</NuxtLink>
      <NuxtLink to="/admin/category">Manage Category</NuxtLink>
      <NuxtLink to="/admin/product">Manage Product</NuxtLink>
      <NuxtLink to="/admin/users">Manage Users</NuxtLink>
      <NuxtLink to="/admin/logout">LogOut</NuxtLink>
    </nav>
  </aside>
</template>

