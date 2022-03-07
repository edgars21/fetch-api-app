<template>
  <div class="mt-8">
    <div class="max-w-screen-xl px-4 mx-auto text-center">  
      <div class="mb-4">
        <ActionForm @FetchRequest="HandleFetch"
          @InputChange="HandleInputChange"
        ></ActionForm>
      </div>  
      <div class="flex flex-col">
        <div class="">
          <div class="py-2 align-middle block min-w-full">
            <div class="shadow border-b border-gray-200 sm:rounded-lg">
              <div class="table-flex min-w-full divide-y divide-gray-200 w-full relative">
                <TableHeading @CategoryChange="HandleCategoryChange" :current-category="currentCategory" :category-list="categoryList"></TableHeading>
                <div class="relative">
                  <TableBody :class="{'opacity-30': loading}" :rows="paginatedRows"></TableBody>
                  <div class="absolute w-full top-0 left-0" :class="{hidden: !loading}">
                    <TableLoading></TableLoading>
                  </div>
                  <div :class="{hidden: !loadingError}">
                     <TableLoadingError></TableLoadingError>
                  </div>                                     
                </div>
              </div>               
            </div>
            <TablePaginaton @PaginationChange="HandlePaginationChange" 
              :current-page="currentPagination" 
              :range="paginationRange" 
              :total="rows.length" 
              :number-of-pages="numberOfPaginationPages">
            </TablePaginaton>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import { ref, nextTick, computed } from "vue";
import ActionForm from './ActionForm';
import TableHeading from './TableHeading';
import TableBody from './TableBody';
import TableLoading from './TableLoading';
import TablePaginaton from './TablePaginaton';
import TableLoadingError from './TableLoadingError';

const getDatafromApi =  async function() {
    const URL = 'https://api.publicapis.org/entries'
    let result = await fetch(URL);
    result = await result.json();
    return result;
}

const gethash = function() {
  let result = false;
  let hash = location.hash;
  if (!hash) return result;
  
  hash = Number(hash.replace('#', ''));
  if (typeof hash === 'number') {
    result = hash;
  } else {
    removeHash();
  }

  return result;
}

const removeHash = function() {
  history.pushState("", document.title, window.location.pathname + window.location.search);  
}

const stayAtPageBottom = function() {
  window.scrollTo(0,document.body.scrollHeight);
}

export default {
  components: {
    ActionForm,
    TableHeading,
    TableBody,
    TableLoading,
    TablePaginaton,
    TableLoadingError,
  },

  setup() {
    let loadedRows = [];
    const rows = ref([]);
    const paginatedRows = ref([]);
    const loading = ref(false);
    const loadingError = ref(false);
    const currentPagination = ref(gethash() || 1 );
    const paginationRange = ref(10);
    const currentCategory = ref('All');
    const categoryList = ref(['All']);
    const inputValue = ref('');

    const numberOfPaginationPages = computed(() => Math.ceil(rows.value.length / paginationRange.value) || 0)


    function setTableRows(data) {
      loadedRows = [...data.entries];
      rows.value = [...data.entries];

      let categories = getCategories(data.entries);
      setCategories(categories);
      setPaginationRows();
    }

    function setPaginationRows() {
      if (currentPagination.value > numberOfPaginationPages.value) {
        currentPagination.value = 1;
      }

      const startPaginateArray = ((currentPagination.value * paginationRange.value) - paginationRange.value);
      const endPaginateArray = startPaginateArray + paginationRange.value;
      paginatedRows.value = rows.value.slice(startPaginateArray, endPaginateArray);
    }

    function filterRowsByCategory(category, listToFilter) {
      if (category === 'All') {
        return listToFilter
      } else {
        return listToFilter.filter(el => el['Category'] ===  category);
      }
    }

    function filterRowsByTitle(string, listToFilter) {
      console.log('filtering by title')
      console.log(listToFilter);
      
      function removeStrongifPresent(el) {
        let title =  el['API'];
        if (!title.includes('<strong>')) return;
        console.log('yes')
        console.log(title.match(/<\/*strong>/g));
        console.log(title.replace(/<\/*strong>/g, ''))
        el['API'] = title.replace(/<\/*strong>/g, '');               
      }

      listToFilter = [...listToFilter]
      listToFilter = filterRowsByCategory(currentCategory.value, listToFilter);     
      
      if (!string) {
        listToFilter.forEach(el => {
          removeStrongifPresent(el);
        })
        return listToFilter;
      }

      let originalRows = listToFilter;
      let filteredRows = [];
      let regexTest;
      //if matches in the begining of first word
      regexTest = new RegExp(`^${string}`);
      originalRows = originalRows.filter(el => {
        removeStrongifPresent(el);
        
        let title = el['API'];

        let result = title.match(regexTest);
        if (result) {
          el['API'] = title.replace(result[0], `<strong>${result[0]}</strong>`);
          filteredRows.push(el)
          return false;
        } else {
          return true;
        }
      })

      //if matches in the begining of second or nth word
      regexTest = new RegExp(` ${string}`);
      originalRows = originalRows.filter(el => {
        let title = el['API'];

        let result = title.match(regexTest);
        if (result) {
          el['API'] = title.replace(result[0], `<strong>${result[0]}</strong>`);
          filteredRows.push(el)
          return false;
        } else {
          return true;
        }
      })

      //if matches anywhere
      regexTest = new RegExp(`${string}`);
      originalRows = originalRows.filter(el => {
        let title = el['API'];

        let result = title.match(regexTest);
        if (result) {
          el['API'] = title.replace(result[0], `<strong>${result[0]}</strong>`);
          filteredRows.push(el)
          return false;
        } else {
          return true;
        }
      })

      originalRows;
      return filteredRows;
    }

    function getCategories(list) {
      let categories = []
      
      list.forEach(el => {
        if (!categories.includes(el['Category'])) {
          categories.push(el['Category'])
        }
      });

      return categories
    }

    function setCategories(list) {
      categoryList.value = ['All', ...list];
    }

    async function HandleFetch() {
      loadingError.value = false;
      loading.value = true;

      if (loadedRows.length) {
        await new Promise(res => {setTimeout(() => {res()}, 500);})
      } else {
        try {
          let result = await getDatafromApi();
          setTableRows(result)
        } catch(er) {
          loadingError.value = true;
          console.log(er);
        }
      }

      rows.value = filterRowsByTitle(inputValue.value, [...loadedRows]);
      setPaginationRows();

      loading.value = false;
    }

    function HandlePaginationChange(data) {
      currentPagination.value = Number(data);
      setPaginationRows();
      nextTick(() => {
        stayAtPageBottom();
      })      
    }

    function HandleCategoryChange(category) {
      let rowsToFilter = [...loadedRows]
      
      if (inputValue.value) {
        rowsToFilter = filterRowsByTitle(inputValue.value, rowsToFilter);
      }

      if (currentCategory.value ===  category) return;
      currentCategory.value = category;
      rows.value = filterRowsByCategory(category, rowsToFilter);
      setPaginationRows();
    }

    function HandleInputChange(data) {
      inputValue.value = data;
    }    

    return {
      rows,
      paginatedRows,
      paginationRange,
      currentPagination,
      numberOfPaginationPages,
      HandlePaginationChange,
      HandleFetch,
      loading,
      loadingError,
      currentCategory,
      categoryList,
      HandleCategoryChange,
      inputValue,
      HandleInputChange,
    }
  },
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

    .table-cell strong {
        color: red;
    }

    .table-cell:nth-child(2) {
        width: 16%;
        flex-grow: 1;
    }    

    .table-cell:nth-child(1) {
        max-width: 140px;
        word-break: break-all;
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