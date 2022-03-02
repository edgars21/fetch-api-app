<template>
    <tbody class="bg-white divide-y divide-gray-200">
        <tr v-for="singleRow in rows" :key="singleRow.email">
        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
            {{ singleRow.name }}
        </td>
        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
            {{ singleRow.title }}
        </td>
        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
            {{ singleRow.email }}
        </td>
        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
            {{ singleRow.role }}
        </td>
        <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
            <a href="#" class="text-indigo-600 hover:text-indigo-900">Edit</a>
        </td>
        </tr>
    </tbody>
</template>


<script>
export default {
    props: [
        'rows',
    ],  
}
</script>