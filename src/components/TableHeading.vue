<template>
    <thead class="bg-gray-50">
        <tr>
        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">API</th>
        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Description</th>
        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Auth</th>
        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">HTTPS</th>
        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Cors</th>
        <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Link</th>
        <th scope="col" 
            class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
            {{ currentCategory === "All" ? 'Category' : currentCategory }}
        </th>
        <th scope="col" class="relative px-6 py-3">
            <span class="sr-only">Edit</span>
        </th>
        </tr>
    </thead>
</template>


<script>
export default {
  props: [
    'current-category',
    'category-list',
  ],

    setup(props) {
        console.log(props.currentCategory)
    }
}
</script>