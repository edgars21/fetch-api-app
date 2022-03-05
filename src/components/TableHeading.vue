<template>
    <div class="table-head-flex bg-gray-50">
        <div class="table-row-flex">
            <div class="table-cell table-cell-header-flex px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider" scope="col">API</div>
            <div class="table-cell table-cell-header-flex px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider" scope="col">Description</div>
            <div class="table-cell table-cell-header-flex px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider" scope="col">Auth</div>
            <div class="table-cell table-cell-header-flex px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider" scope="col">HTTPS</div>
            <div class="table-cell table-cell-header-flex px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider" scope="col">Cors</div>
            <div class="table-cell table-cell-header-flex px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider" scope="col">Link</div>
            <div class="table-cell table-cell-header-flex" scope="col">
                <CategoryDropDown 
                    @CategoryChange="PipeCategoryChange" 
                    :current-category="currentCategory" 
                    :category-list="categoryList">
                </CategoryDropDown>
            </div>
        </div>
    </div>
</template>


<script>
import CategoryDropDown from './CategoryDropDown';

export default {
    components: {
        CategoryDropDown
    },

    props: [
        'current-category',
        'category-list',
    ],

    setup(props, context) {
        function PipeCategoryChange(data) {
            context.emit('CategoryChange', data);
        }
        
        return { PipeCategoryChange };
    }
}
</script>


<style>
    .table-flex {

    }

    .table-body-flex {

    }

    .table-head-flex {

    }

    .table-row-flex {
        display: flex;
    }

    .table-cell {
        width: 14%;
        flex-shrink: 0;
        flex-grow: 1;
    }

    .table-cell:nth-child(2) {
        width: 16%;
        flex-grow: 1;
    }    

    .table-cell:nth-child(1) {
        max-width: 140px;
    }    

    .table-cell:nth-child(2) {
        max-width: unset;
    }

    .table-cell:nth-child(3) {
        max-width: 100px;
    }
    
    .table-cell:nth-child(4) {
        max-width: 90px;
    }
    
    .table-cell:nth-child(5) {
        max-width: 90px;
    }
    
    .table-cell:nth-child(6) {
        max-width: 200px;
        word-break: break-all;
    }
    
    .table-cell:nth-child(7) {
        max-width: 200px;
    }    

    .table-cell-header-flex {

    }    

    .table-cell-data-flex {

    }
</style>