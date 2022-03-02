<template>        
    <div class="flex -mx-4">
        <div class="w-40 mx-4"><input class="w-full py-2 px-6 border border-gray" placeholder="All"></div>
        <div class="mx-4"><button @click="handleFetchClick" class="bg-blue-700 hover:bg-blue-600 text-white text-bold py-2 px-6 rounded">Fetch</button></div>
    </div>
</template>

<script>
export default {
    setup(props, context) {
        function handleFetchClick() {
            context.emit('FetchRequest');
        }
        return {handleFetchClick};
    }
}
</script>