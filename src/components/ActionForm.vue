<template>
    <form @submit.prevent="handleFormSubmit">        
        <div class="flex -mx-4">
            <div class="w-40 mx-4"><input @input="HandleInputChange" v-model="inputValue" class="w-full py-2 px-6 border border-gray" placeholder="All"></div>
            <div class="mx-4"><button type="submit" class="bg-blue-700 hover:bg-blue-600 text-white text-bold py-2 px-6 rounded">Fetch</button></div>
        </div>
    </form>
</template>

<script>
import { ref } from "vue";

export default {
    setup(props, context) {
        const inputValue = ref('');

        function HandleInputChange() {
            context.emit('InputChange', inputValue.value);

        }

        function handleFormSubmit() {
            context.emit('FetchRequest');
        }        

        return { inputValue, HandleInputChange, handleFormSubmit };
    }
}
</script>