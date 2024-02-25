<template>
    <div class="flex flex-col w-full">
        <label for="price" class="block text-sm font-medium leading-6 text-gray-900">{{ title }}</label>
        <div class="relative rounded-md shadow-sm">
            <input v-model="activeItem" type="number" placeholder="Введите число" class="block w-full rounded-md border-0 py-1.5 pr-22 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6 [&::-webkit-inner-spin-button]:appearance-none [&::-webkit-outer-spin-button]:m-0 [appearance:textfield]">
            <div class="absolute inset-y-0 right-2 flex items-center text-sm">
                <button @click="setFilter">Применить</button>
            </div>
        </div>
    </div>
</template>


<script setup>

    import {Inertia} from "@inertiajs/inertia";

    const props = defineProps({
        filterName: String,
        title: String,
        activeItem: Number,
    })

    import {ref} from "vue";

    const clicked = ref(false)

    function setFilter() {
        const params = route().params;

        props.activeItem == '' ?
            delete params[props.filterName] :
            params[props.filterName] = props.activeItem

        params['page'] = 1

        Inertia.get(route(route().current(), params))
    }

</script>
