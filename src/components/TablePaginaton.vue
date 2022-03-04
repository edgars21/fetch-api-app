/* eslint-disable */
<!-- This example requires Tailwind CSS v2.0+ -->
<template>
  <div class="bg-white px-4 py-3 flex items-center justify-between border-t border-gray-200 sm:px-6">
    <div class="flex-1 flex justify-between sm:hidden">
      <a href="#" class="relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50"> Previous </a>
      <a href="#" class="ml-3 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50"> Next </a>
    </div>
    <div class="hidden sm:flex-1 sm:flex sm:items-center sm:justify-between">
      <div>
        <p v-if="total" class="text-sm text-gray-700">
          Showing
          {{ ' ' }}
          <span class="font-medium">{{ currentPage }}</span>
          {{ ' ' }}
          to
          {{ ' ' }}
          <span class="font-medium">{{ paginationPages.length }}</span>
          {{ ' ' }}
          of
          {{ ' ' }}
          <span class="font-medium">{{ total }}</span>
          {{ ' ' }}
          results
        </p>
        <p v-else class="text-sm text-gray-700"></p>
      </div>
      <div>
        <nav class="relative z-0 inline-flex rounded-md shadow-sm -space-x-px" aria-label="Pagination">
          <a :class="{'pointer-events-none': (currentPage === 1)}" @click="HandlePaginationClick" :data-pagination-value="Number(currentPage) - 1" :href="'#' + (Number(currentPage) - 1)" class="relative inline-flex items-center px-2 py-2 rounded-l-md border border-gray-300 bg-white text-sm font-medium text-gray-500 hover:bg-gray-50">
            <span class="sr-only">Previous</span>
            <ChevronLeftIcon class="h-5 w-5" aria-hidden="true" />
          </a>
          
          <template v-for="paginationPage, index in paginationShowPages" :key="index">
            <a @click="HandlePaginationClick"   
              :data-pagination-value="paginationPage" 
              :href="'#' + paginationPage" 
              aria-current="page" 
              class="relative inline-flex items-center px-4 py-2 border text-sm font-medium"
              :class="paginationPage == currentPage ? 'z-10 bg-indigo-50 border-indigo-500 text-indigo-600': 'bg-white border-gray-300 text-gray-500 hover:bg-gray-50'">
                {{ paginationPage }}
            </a>
          </template>          
          
          <a :class="{'pointer-events-none': (currentPage == paginationPages.length)}" @click="HandlePaginationClick" :data-pagination-value="Number(currentPage) + 1" :href="'#' + (Number(currentPage) + 1)" class="relative inline-flex items-center px-2 py-2 rounded-r-md border border-gray-300 bg-white text-sm font-medium text-gray-500 hover:bg-gray-50">
            <span class="sr-only">Next</span>
            <ChevronRightIcon class="h-5 w-5" aria-hidden="true" />
          </a>
        </nav>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
import { ChevronLeftIcon, ChevronRightIcon } from '@heroicons/vue/solid';
import { computed } from "vue";

const setHash = function(num) {
  window.location.hash = num;
}

export default {
  props: [
    'current-page',
    'range',
    'total',
  ],

  components: {
    ChevronLeftIcon,
    ChevronRightIcon,
  },

  setup(props, context) {
    const paginationShowLimit = 7;

    const paginationPages = computed(() => {
      const numberOfPages = Math.ceil(props.total / props.range) || 0;
      const arrayOfPages = [...Array(numberOfPages).keys()].map(x => ++x);
      return arrayOfPages;
    });

    const paginationShowPages = computed(() => {
      let buildPagination = [];  
      let needTail = false;
      let needHead = false;

      if (paginationPages.value.length <= paginationShowLimit) {
        buildPagination =  paginationPages.value.slice(0, paginationPages.length);
      } else {
        if (props.currentPage == 1) {
          needTail = true;
        } else if (props.currentPage >= 4 ) {
          buildPagination.push(...paginationPages.value.slice(props.currentPage - 4, props.currentPage -1));
        } else {
          buildPagination.push(...paginationPages.value.slice(0, props.currentPage -1));
          needTail = true;
        }

        buildPagination.push(+props.currentPage)

        if (props.currentPage >= paginationPages.value.length - 3) {
          buildPagination.push(...paginationPages.value.slice(props.currentPage));
          needHead = true;
        } else {
          buildPagination.push(...paginationPages.value.slice(Number(props.currentPage), Number(props.currentPage) + 3));
        }
        
        if (needTail) {
          let tailNeed = paginationShowLimit - buildPagination.length;
          let buildTail = Array(tailNeed);
          buildPagination.push(...buildTail);
          buildPagination = buildPagination.map((_, idx) => buildPagination[0] + idx)
        }

        if (needHead) {
          let headNeed = paginationShowLimit - buildPagination.length;
          let buildHead = Array(headNeed);
          buildPagination.reverse();
          buildPagination.push(...buildHead);
          buildPagination = buildPagination.map((_, idx) => buildPagination[0] - idx);
          buildPagination.reverse();
        }
      }

      return buildPagination;
    });

    function HandlePaginationClick(event) {
      event.preventDefault();
      const nextPgination = event.currentTarget.getAttribute('data-pagination-value')
      setHash(nextPgination);
      context.emit('PaginationChange', nextPgination);
    }

    return { paginationPages, paginationShowPages, HandlePaginationClick }
  }
}
</script>