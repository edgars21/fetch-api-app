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