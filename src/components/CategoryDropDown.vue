<template>
  <Menu as="div" class="relative block text-left">
    <div>
      <MenuButton class="inline-flex justify-start items-center px-6 py-3 w-full text-left text-xs font-medium text-gray-500 uppercase tracking-wider hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-gray-100 focus:ring-indigo-500">
        {{ currentCategory === "All" ? 'Category' : currentCategory }}
        <ChevronDownIcon class="h-5 w-5" aria-hidden="true" />
      </MenuButton>
    </div>

    <transition enter-active-class="transition ease-out duration-100" enter-from-class="transform opacity-0 scale-95" enter-to-class="transform opacity-100 scale-100" leave-active-class="transition ease-in duration-75" leave-from-class="transform opacity-100 scale-100" leave-to-class="transform opacity-0 scale-95">
      <MenuItems class="origin-top-right absolute right-0 mt-2 w-56 rounded-md shadow-lg bg-white ring-1 ring-black ring-opacity-5 focus:outline-none">
        <div class="py-1 max-h-[300px] overflow-y-scroll">
          <MenuItem v-for="category, index in categoryList" :key="index" v-slot="{ active }">
            <a @click="HandleDropDownClick" :data-category-value="category" href="" :class="[active ? 'bg-gray-100 text-gray-900' : 'text-gray-700', 'block px-4 py-2 text-sm']">
                {{ category }}
            </a>
          </MenuItem>
        </div>
      </MenuItems>
    </transition>
  </Menu>
</template>

<script>
import { Menu, MenuButton, MenuItem, MenuItems } from '@headlessui/vue';
import { ChevronDownIcon } from '@heroicons/vue/solid';

export default {
  components: {
    Menu,
    MenuButton,
    MenuItem,
    MenuItems,
    ChevronDownIcon,
  },

  props: [
    'current-category',
    'category-list',
  ],

  setup(props, context) {
    function HandleDropDownClick(event) {
      event.preventDefault();
      const category = event.currentTarget.getAttribute('data-category-value');
      context.emit('CategoryChange', category);
    }

    return { HandleDropDownClick }
  }
}
</script>