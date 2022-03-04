<template>
  <div class="mt-8">
    <div class="max-w-screen-xl px-4 mx-auto text-center">  
      <div class="mb-4">
        <ActionForm @FetchRequest="HandleFetch"></ActionForm>
      </div>  
      <div class="flex flex-col">
        <div class="overflow-x-auto">
          <div class="py-2 align-middle block min-w-full">
            <div class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">
              <table class="min-w-full divide-y divide-gray-200 w-full relative">
                <TableHeading :current-category="currentCategory" :category-list="categoryList"></TableHeading>
                <TableBody :rows="paginatedRows"></TableBody>
              </table>
              <div :class="{hidden: !loading}">
                <TableLoading></TableLoading>
              </div>
             <div :class="{hidden: !loadingError}">
                <TableLoadingError></TableLoadingError>
              </div>                
            </div>
            <TablePaginaton @PaginationChange="HandlePaginationChange" :current-page="currentPagination" :range="paginationRange" :total="rows.length"></TablePaginaton>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import { ref, nextTick } from "vue";
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
    const rows = ref([]);
    const paginatedRows = ref([]);
    const loading = ref(false);
    const loadingError = ref(false);
    const currentPagination = ref(gethash() || 1 );
    const paginationRange = ref(10);
    const currentCategory = ref('All');
    const categoryList = ref(['All']);

    function setPaginationRows() {
      const startPaginateArray = ((currentPagination.value * paginationRange.value) - paginationRange.value);
      const endPaginateArray = startPaginateArray + paginationRange.value;
      paginatedRows.value = rows.value.slice(startPaginateArray, endPaginateArray);
    }

    function setTableRows(data) {
      rows.value = data.entries;
      console.log(getCategories(data.entries))
      let categories = getCategories(data.entries);
      setCategories(categories);
      setPaginationRows();
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
      try {
        let result = await getDatafromApi();
        setTableRows(result)
      } catch(er) {
        loadingError.value = true;
        console.log(er);
      }
      loading.value = false;
    }

    function HandlePaginationChange(data) {
      currentPagination.value = data;
      setPaginationRows();
      nextTick(() => {
        stayAtPageBottom();
      })      
    }

    return {
      rows,
      paginatedRows,
      paginationRange,
      currentPagination,
      HandlePaginationChange,
      HandleFetch,
      loading,
      loadingError,
      currentCategory,
    }
  },
}
</script>