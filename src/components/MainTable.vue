<template>
  <div class="mt-8">
    <div class="max-w-screen-xl px-4 mx-auto text-center">  
      <div class="mb-4">
        <ActionForm @FetchRequest="HandleFetch"></ActionForm>
      </div>  
      <div class="flex flex-col">
        <div class="-my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
          <div class="py-2 align-middle inline-block min-w-full sm:px-6 lg:px-8">
            <div class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">
              <table class="min-w-full divide-y divide-gray-200 w-full">
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
import { ref } from "vue";
import ActionForm from './ActionForm';
import TableHeading from './TableHeading';
import TableBody from './TableBody';

const getDatafromApi =  async function() {
    const URL = 'https://api.publicapis.org/entries'
    let result = await fetch(URL);
    result = await result.json();
    return result;
}

export default {
  components: {
    ActionForm,
    TableHeading,
    TableBody,
  },

  setup() {
    const rows = ref([]);

    async function HandleFetch() {
      try {
        let result = await getDatafromApi();
        rows.value = result.entries;
      } catch(er) {
        console.log(er);
      }
    }

    return {
      rows,
      HandleFetch,
    }
  },
}
</script>