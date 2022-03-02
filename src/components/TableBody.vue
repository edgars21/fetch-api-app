<template>
    <tbody class="bg-white divide-y divide-gray-200">
        <tr class="text-left" v-for="singleRow in rows" :key="singleRow.email">
        <td class="px-6 py-4 text-sm font-medium text-gray-900">
            {{ singleRow['API'] }}
        </td>
        <td class="px-6 py-4 text-sm text-gray-500">
            {{ singleRow['Description'] }}
        </td>
        <td class="px-6 py-4 text-sm text-gray-500">
            {{ singleRow['Auth'] }}
        </td>
        <td class="px-6 py-4 text-sm text-gray-500">
            {{ singleRow['HTTPS'] }}
        </td>
        <td class="px-6 py-4 text-sm text-gray-500">
            {{ singleRow['Cors'] }}
        </td>
        <td class="px-6 py-4 text-sm text-gray-500">
            {{ singleRow['Link'] }}
        </td>
        <td class="px-6 py-4 text-sm text-gray-500">
            {{ singleRow['Category'] }}
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