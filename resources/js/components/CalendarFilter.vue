<template>
    <div class="flex flex-col gap-0.5 w-full">
        <span class="block text-sm font-medium leading-6 text-gray-900">{{title}}</span>
        <VueDatePicker format="dd.MM.yyyy" placeholder="Введите дату" v-model="calendarDate" :range="isRange" @update:model-value="handleDate"/>
    </div>

</template>

<script setup>
    import VueDatePicker from '@vuepic/vue-datepicker';
    import '@vuepic/vue-datepicker/dist/main.css'
    import {ref} from "vue";
    import {Inertia} from "@inertiajs/inertia";
    import formatDate from "../utils/formatDate";

    const props = defineProps({
        isRange: {
            required: false,
            default: false,
            type: Boolean,
        },
        filterName: {
            required: true,
            type: String
        },
        title: String,
        date: {
            default: null
        }
    })


    const calendarDate = ref(null)

    if (props.date && !props.isRange) {
        const [day, month, year] =  props.date.split('.');

        calendarDate.value = new Date(year, month - 1, day);
    }
    else if (props.isRange && props.date) {
        let [day, month, year] =  props.date[0].split('.');

        calendarDate.value = []

        calendarDate.value[0] = new Date(year, month - 1, day);

        [day, month, year] =  props.date[1].split('.');

        calendarDate.value[1] = new Date(year, month - 1, day);
    }

    const clicked = ref(false)

    const handleDate = () => {
        const params = route().params;

        if (calendarDate.value) {
            if (props.isRange) {
                if (params[props.filterName]) {
                    delete params[props.filterName]
                }

                params[props.filterName + '[0]'] = formatDate(calendarDate.value[0])
                params[props.filterName + '[1]'] = formatDate(calendarDate.value[1])
            }
            else {
                params[props.filterName] = formatDate(calendarDate.value)
            }
        }
        else {
            delete params[props.filterName]
        }

        params['page'] = 1

        Inertia.get(route(route().current(), params))
    }

</script>
