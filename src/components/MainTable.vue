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

function getCategoriesFromRows(list) {
  let categories = []
  
  list.forEach(el => {
    if (!categories.includes(el['Category'])) {
      categories.push(el['Category'])
    }
  });

  return categories;
}

function filterRowsByCategory(category, listToFilter) {
  if (category === 'All') {
    return listToFilter
  } else {
    return listToFilter.filter(el => el['Category'] ===  category);
  }
}

function filterRowsByTitle(string, listToFilter) {
  if (!string) {
    listToFilter.forEach(el => {
      cleanMarkersIfPresent(el);
    })
    return listToFilter;
  }

  let originalRows = listToFilter;
  let filteredRows = [];
  let regexTest;

  //if matches in the begining of first word
  regexTest = new RegExp(`^${string}`);
  originalRows = filterAgainstRegex(originalRows, filteredRows, regexTest, true);

  //if matches in the begining of second or nth word
  regexTest = new RegExp(` ${string}`);
  originalRows = filterAgainstRegex(originalRows, filteredRows, regexTest);

  //if matches anywhere
  regexTest = new RegExp(`${string}`);
  originalRows = filterAgainstRegex(originalRows, filteredRows, regexTest);

  originalRows;
  return filteredRows;
}

function filterAgainstRegex(list, filteredRows, regex, clean) {
  list = list.filter(el => {
    if (clean) {
      cleanMarkersIfPresent(el);
    }
    
    const title = el['API'];
    const result = title.match(regex);
    if (result) {
      el['API'] = title.replace(result[0], `<strong>${result[0]}</strong>`);
      filteredRows.push(el)
      return false;
    } else {
      return true;
    }
  }) ;

  return list;
}

function cleanMarkersIfPresent(el) {
  let title =  el['API'];
  if (!title.includes('<strong>')) return;
  el['API'] = title.replace(/<\/*strong>/g, '');               
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

    const numberOfPaginationPages = computed(() => Math.ceil(rows.value.length / paginationRange.value) || 0);
    let inputEventTrottle = [];
    async function initialTalbeInit() {
      requestTableLoading(true);
      try {
        let result = await getDatafromApi();
        loadedRows = [...result.entries];
        rows.value = [...result.entries];
        filterTable();
      } catch(er) {
        loadingError.value = true;
        console.log(er);
      }
      requestTableLoading(false);     
    }

    function filterTable() {
      requestTableLoading(true);
      let newTableList = [...loadedRows];
      newTableList = filterRowsByTitle(inputValue.value, newTableList);
      syncCategories(newTableList);
      newTableList = filterRowsByCategory(currentCategory.value, newTableList);
      rows.value = newTableList;
      setPaginationRows();
      requestTableLoading(false);
    }

    function syncCategories(currentList) {
      const categoriesList = getCategoriesFromRows(currentList);
      categoryList.value = ['All', ...categoriesList];
    }

    function setPaginationRows() {
      if (currentPagination.value > numberOfPaginationPages.value) {
        currentPagination.value = 1;
      }

      const startPaginateArray = ((currentPagination.value * paginationRange.value) - paginationRange.value);
      const endPaginateArray = startPaginateArray + paginationRange.value;
      paginatedRows.value = rows.value.slice(startPaginateArray, endPaginateArray);
    }

    function requestTableLoading(state) {
      if (state) {
        loadingError.value = false;
        loading.value = true;
      } else if (!state) {
        setTimeout(() => {
          loading.value = false;
        }, 500)
      }
    }

    async function HandleFetch() {
      if (loadedRows.length) {
        filterTable();
        await new Promise(res => {setTimeout(() => {res()}, 500);})
      } else {
        initialTalbeInit();
      }
    }

    function HandlePaginationChange(data) {
      currentPagination.value = Number(data);
      setPaginationRows();
      nextTick(() => {
        stayAtPageBottom();
      })      
    }

    function HandleCategoryChange(category) {
      if (currentCategory.value ===  category) return;
      currentCategory.value = category;
      filterTable();
    }

    function HandleInputChange(data) {
      inputValue.value = data;
      TrottleTableUpdateAfterInputChange();     
    }

    function TrottleTableUpdateAfterInputChange() {
      requestTableLoading(true);
      inputEventTrottle.push(true);
      if (inputEventTrottle.length < 1 ) {
        inputEventTrottle.pop(), filterTable();
      } else {
        setTimeout(() => {
          if (inputEventTrottle.length > 0) {
            inputEventTrottle.pop(),filterTable();
            inputEventTrottle = [];
          }
        }, 300)
      }
      requestTableLoading(false);           
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