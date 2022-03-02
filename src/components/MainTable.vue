<template>
  <div class="mt-8">
    <div class="max-w-screen-xl px-4 mx-auto text-center">  
      <div class="flex flex-col">
        <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
          <div class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
            <div class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">
              <table class="min-w-full divide-y divide-gray-200">
                <TableHeading></TableHeading>
                <TableBody :rows="rows"></TableBody>
              </table>  
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
const rows = [
  { name: 'Jane Cooper', title: 'Regional Paradigm Technician', role: 'Admin', email: 'jane.cooper@example.com' },
  // More people...
]

import TableHeading from './TableHeading';
import TableBody from './TableBody';

export default {
  components: {
    TableHeading,
    TableBody,
  },

  setup() {
    return {
      rows,
    }
  },
}
</script>