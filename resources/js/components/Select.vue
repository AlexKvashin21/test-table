<template>
    <div class="flex flex-col w-full">
        <label id="listbox-label" class="block text-sm font-medium leading-6 text-gray-900">{{title}}</label>
        <div class="relative">
            <button @click="clicked = !clicked" type="button" class="cursor-pointer relative w-full rounded-md bg-white py-1.5 pl-3 pr-10 text-left text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:outline-none focus:ring-2 focus:ring-indigo-500 sm:text-sm sm:leading-6" aria-haspopup="listbox" aria-expanded="true" aria-labelledby="listbox-label">
      <span class="flex items-center">
        <span v-if="activeItem" class="block truncate">{{options[activeItem]}}</span>
        <span v-else class="block truncate">Выбрать</span>
      </span>
                <span class="pointer-events-none absolute inset-y-0 right-0 ml-3 flex items-center pr-2">
        <svg class="h-5 w-5 text-gray-400" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
          <path fill-rule="evenodd" d="M10 3a.75.75 0 01.55.24l3.25 3.5a.75.75 0 11-1.1 1.02L10 4.852 7.3 7.76a.75.75 0 01-1.1-1.02l3.25-3.5A.75.75 0 0110 3zm-3.76 9.2a.75.75 0 011.06.04l2.7 2.908 2.7-2.908a.75.75 0 111.1 1.02l-3.25 3.5a.75.75 0 01-1.1 0l-3.25-3.5a.75.75 0 01.04-1.06z" clip-rule="evenodd" />
        </svg>
      </span>
            </button>
            <ul v-if="clicked" class="absolute z-10 mt-1 max-h-56 w-full overflow-auto rounded-md bg-white py-1 text-base shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none sm:text-sm" tabindex="-1" role="listbox" aria-labelledby="listbox-label" aria-activedescendant="listbox-option-3">
                <li v-for="(option, id) in options" :key="index+'-'+id" @click="setFilter(id)" class="text-gray-900 hover:text-gray-950 cursor-pointer relative select-none py-2 pl-3 pr-9" id="listbox-option-0" role="option">
                    <div class="flex items-center">
                        <span class="font-normal ml-3 block truncate">{{option}}</span>
                    </div>
                    <span class="text-indigo-600 absolute inset-y-0 right-0 flex items-center pr-4">
                        <svg v-if="route().params[filterName] === id" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                            <path fill-rule="evenodd" d="M16.704 4.153a.75.75 0 01.143 1.052l-8 10.5a.75.75 0 01-1.127.075l-4.5-4.5a.75.75 0 011.06-1.06l3.894 3.893 7.48-9.817a.75.75 0 011.05-.143z" clip-rule="evenodd" />
                        </svg>
                    </span>
                </li>
            </ul>
        </div>
    </div>
</template>

<script setup>

    import {Inertia} from "@inertiajs/inertia";

    const props = defineProps({
        options: Array,
        filterName: String,
        title: String,
        activeItem: Number,
    })

    import {ref} from "vue";

    const clicked = ref(false)

    function setFilter(id) {
        const params = route().params;

        params[props.filterName] === id ?
            delete params[props.filterName] :
            params[props.filterName] = id

        params['page'] = 1

        Inertia.get(route(route().current(), params))
    }

</script>
