<template>
    <div class="table-body-flex bg-white divide-y divide-gray-200">
        <div class="table-row-flex text-left" v-for="singleRow in rows" :key="singleRow.email">
            <div v-html="singleRow['API']" class="table-cell table-cell-data-flex px-6 py-4 text-sm font-medium text-gray-900">
            </div>
            <div class="table-cell table-cell-data-flex px-6 py-4 text-sm text-gray-500">
                {{ singleRow['Description'] }}
            </div>
            <div class="table-cell table-cell-data-flex px-6 py-4 text-sm text-gray-500">
                {{ singleRow['Auth'] }}
            </div>
            <div class="table-cell table-cell-data-flex px-6 py-4 text-sm text-gray-500">
                {{ singleRow['HTTPS'] }}
            </div>
            <div class="table-cell table-cell-data-flex px-6 py-4 text-sm text-gray-500">
                {{ singleRow['Cors'] }}
            </div>
            <div class="table-cell table-cell-data-flex px-6 py-4 text-sm text-gray-500">
                {{ singleRow['Link'] }}
            </div>
            <div class="table-cell table-cell-data-flex px-6 py-4 text-sm text-gray-500">
                {{ singleRow['Category'] }}
            </div>                
        </div>
    </div>
</template>


<script>
export default {
    props: [
        'rows',
    ],  
}
</script>