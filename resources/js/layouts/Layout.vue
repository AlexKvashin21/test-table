<template>
    <Head :title="title"/>

    <nav v-if="$page.props.user" class="bg-gray-800">
        <div class="max-w-7xl mx-auto">
            <div class="relative flex items-center flex-col sm:flex-row  sm:items-center p-2 sm:justify-between min-h-16">
                <div class="flex-1 flex items-center  sm:items-stretch sm:justify-start">
                    <div class="flex space-x-4">
                        <Link v-for="(item, index) in navigation" :href="route(item.route)" :key="'navigation-'+index" class="text-gray-300 p-2 hover:bg-gray-700 hover:text-white rounded-md text-sm font-medium">
                            {{item.name}}
                        </Link>
                    </div>
                </div>
                <div v-if="$page.props.user" class="flex flex-col sm:flex-row items-center text-gray-300 gap-x-5">
                    {{ $page.props.user.email }}

                    <div class="hover:bg-gray-700 hover:text-white p-2 rounded">
                        <Link :href="route('logout')" method="post">Выйти</Link>
                    </div>
                </div>
            </div>
        </div>
    </nav>



    <div class="min-h-full">
        <main>
            <div class="mx-auto max-w-7xl py-6 sm:px-6 lg:px-8">
                <slot></slot>
            </div>
        </main>
    </div>

</template>

<script setup>
import { ref } from 'vue'
import {Head, Link} from "@inertiajs/inertia-vue3";
import {
    Dialog,
    DialogPanel,
    Disclosure,
    DisclosureButton,
    DisclosurePanel,
    Popover,
    PopoverButton,
    PopoverGroup,
    PopoverPanel,
} from '@headlessui/vue'
import {
    ArrowPathIcon,
    Bars3Icon,
    ChartPieIcon,
    CursorArrowRaysIcon,
    FingerPrintIcon,
    SquaresPlusIcon,
    XMarkIcon,
} from '@heroicons/vue/24/outline'
import { ChevronDownIcon, PhoneIcon, PlayCircleIcon } from '@heroicons/vue/20/solid'

defineProps({
    title: String
})

const mobileMenuOpen = ref(false)

const navigation = [
    { name: 'Таблица', route: 'table', current: true },
]
</script>
