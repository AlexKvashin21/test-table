<template>
    <div class="p-2">
        <div class="flex flex-wrap lg:flex-nowrap gap-2">
            <FilterSelect :options="offices" :active-item="filters.office" filter-name="office" title="Офис"/>
            <FilterSelect :options="managers" :active-item="filters.manager_id" filter-name="manager_id" title="Менеджер"/>
            <FilterSelect :options="lawyers" :active-item="filters.lawyer_id" filter-name="lawyer_id" title="Юрист"/>
            <InputFilter title="ID клиента" :active-item="filters.client_id" filter-name="client_id"/>
        </div>

        <div class="flex flex-wrap lg:flex-nowrap mt-2 gap-2">
            <CalendarFilter :is-range="false" filter-name="date" title="Месяц оплаты" :date="route().params.date"/>
            <CalendarFilter :is-range="true" filter-name="dates_transfer" title="Передачи клиента" :date="route().params.dates_transfer"/>
            <CalendarFilter :is-range="true" filter-name="dates_pay" title="Оплаты" :date="route().params.dates_pay"/>
        </div>
    </div>

    <div class="flex flex-col">
        <div class="overflow-x-auto sm:-mx-6 lg:-mx-12">
            <div class="py-2 align-middle inline-block min-w-full px-2 sm:px-6">
                <div v-if="payPlanItem.total > 0" class="shadow overflow-hidden border-b border-gray-200 sm:rounded-lg">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                        <tr>
                            <th v-if="role !== 'Manager'" scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Офис и менеджер
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Юрист
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                ФИО Клиента
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Программа
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Дата регистрации
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                План
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Стоимость программы
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                В месяц
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Даты оплаты
                            </th>
                            <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                Дата передачи
                            </th>
                        </tr>
                        </thead>
                        <tbody class="bg-white divide-y divide-gray-200">
                        <tr v-for="item in payPlanItem.data" :key="item.id">
                            <td  v-if="role !== 'Manager'" class="px-6 py-4 whitespace-nowrap">
                                <div @click="openChangeLawyerAndManagerModal(item.id, 'manager', 'Менеджер', item.manager.id)" :class="role === 'Lawyer' ? 'cursor-pointer hover:text-green-500' : ''" class="text-sm font-medium text-gray-900">
                                    {{ item.manager.office }} {{' - '}} {{item.manager.name}}
                                </div>
                            </td>

                            <td class="px-6 py-4 whitespace-nowrap">
                                <div @click="openChangeLawyerAndManagerModal(item.id, 'lawyer', 'Юрист', item.lawyer.id)" class="cursor-pointer hover:text-green-500 text-sm font-medium text-gray-900">
                                    {{ item.lawyer.name }}
                                </div>
                            </td>

                            <td class="px-6 py-4 whitespace-nowrap">
                                <div @click="openChangeNameModal(item.id, item.client_name)" class="cursor-pointer hover:text-green-500 text-sm font-medium text-gray-900">
                                    {{ item.client_name }}
                                </div>
                            </td>

                            <td class="px-6 py-4 whitespace-nowrap">
                                <div class="text-sm font-medium text-gray-900">
                                    {{ item.program_type }}
                                </div>
                            </td>

                             <td class="px-6 py-4 whitespace-nowrap">
                                <div class="text-sm font-medium text-gray-900">
                                    {{ item.date_register }}
                                </div>
                            </td>

                            <td class="px-6 py-4 whitespace-nowrap">
                                <div class="text-sm font-medium text-gray-900">
                                    {{ item.program_plan + '/' + item.all_payments }}
                                </div>
                            </td>

                            <td class="px-6 py-4 whitespace-nowrap">
                                <div class="text-sm font-medium text-gray-900">
                                    {{ item.program_price + '/' + item.all_payments }}
                                </div>
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap">
                                <div @click="openAllPaymentsModal(item.list_full_remmitances)" :class="item.list_full_remmitances.length > 0 ? 'cursor-pointer hover:text-green-500' : ''" class="text-sm font-medium text-gray-900">
                                    {{ item.program_payment_per_month + '/' + (item.payment_select.length > 0 ? item.payment_select[0].amount : 0)}}
                                </div>
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap">
                                <div @click="openChangeDatePaymentModal(item.id, item.program_first_date, item.program_second_date)" :class="role === 'Lawyer' ? 'cursor-pointer hover:text-green-500' : ''" class="text-sm font-medium text-gray-900">
                                     {{item.program_first_date}}
                                    {{' - '}}
                                    {{item.program_second_date}}
                                </div>
                            </td>
                            <td class="px-6 py-4 whitespace-nowrap">
                                <div class="text-sm font-medium text-gray-900">
                                     {{item.client_handover_date}}
                                </div>
                            </td>


                        </tr>
                        </tbody>
                    </table>

                    <Pagination :links="payPlanItem.links" />
                </div>

                <div v-else class="text-center font-bold text-xl">
                    Пользователей пока нет
                </div>
            </div>
        </div>
    </div>

    <Modal
        :open="changeLawyerAndManagerModalOptions.isOpen"
        @close="changeLawyerAndManagerModalOptions.isOpen = false; changeLawyerAndManagerModalOptions.clickedSelect = false"
    >
        <template v-slot:body>
            <div class="sm:flex sm:items-start">
                <div class="mt-3 text-center sm:mt-0 sm:text-left w-full">
                    <h3 class="text-base font-semibold leading-6 text-gray-900 mb-2">
                        {{ changeLawyerAndManagerModalOptions.title }}
                    </h3>
                    <div class="flex flex-col w-full">
                        <div class="relative">
                            <button @click="changeLawyerAndManagerModalOptions.clickedSelect = !changeLawyerAndManagerModalOptions.clickedSelect" type="button" class="cursor-pointer relative w-full rounded-md bg-white py-1.5 pl-3 pr-10 text-left text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:outline-none focus:ring-2 focus:ring-indigo-500 sm:text-sm sm:leading-6" aria-haspopup="listbox" aria-expanded="true" aria-labelledby="listbox-label">
                                <span class="flex items-center">
                                    <span v-if="changeLawyerAndManagerModalOptions.employee_id" class="block truncate">
                                        {{changeLawyerAndManagerModalOptions.employee_type === 'manager' ? managers[changeLawyerAndManagerModalOptions.employee_id] : lawyers[changeLawyerAndManagerModalOptions.employee_id]}}
                                    </span>
                                    <span v-else class="block truncate">Выбрать</span>
                                </span>
                                <span class="pointer-events-none absolute inset-y-0 right-0 ml-3 flex items-center pr-2">
                                    <svg class="h-5 w-5 text-gray-400" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                        <path fill-rule="evenodd" d="M10 3a.75.75 0 01.55.24l3.25 3.5a.75.75 0 11-1.1 1.02L10 4.852 7.3 7.76a.75.75 0 01-1.1-1.02l3.25-3.5A.75.75 0 0110 3zm-3.76 9.2a.75.75 0 011.06.04l2.7 2.908 2.7-2.908a.75.75 0 111.1 1.02l-3.25 3.5a.75.75 0 01-1.1 0l-3.25-3.5a.75.75 0 01.04-1.06z" clip-rule="evenodd" />
                                    </svg>
                                </span>
                            </button>
                            <ul v-if="changeLawyerAndManagerModalOptions.clickedSelect" class="absolute z-10 mt-1 max-h-10 w-full overflow-auto rounded-md bg-white py-1 text-base shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none sm:text-sm" tabindex="-1" role="listbox" aria-labelledby="listbox-label" aria-activedescendant="listbox-option-3">
                                <li v-for="(option, id) in changeLawyerAndManagerModalOptions.employee_type === 'manager' ? managers : lawyers" :key="'employee-'+id" @click="changeLawyerAndManagerModalOptions.employee_id = id" class="text-gray-900 hover:text-gray-950 cursor-pointer relative select-none py-2 pl-3 pr-9" id="listbox-option-0" role="option">
                                    <div class="flex items-center">
                                        <span class="font-normal ml-3 block truncate">{{option}}</span>
                                    </div>
                                    <span class="text-indigo-600 absolute inset-y-0 right-0 flex items-center pr-4">
                                        <svg v-if="changeLawyerAndManagerModalOptions.employee_id == id" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor" aria-hidden="true">
                                            <path fill-rule="evenodd" d="M16.704 4.153a.75.75 0 01.143 1.052l-8 10.5a.75.75 0 01-1.127.075l-4.5-4.5a.75.75 0 011.06-1.06l3.894 3.893 7.48-9.817a.75.75 0 011.05-.143z" clip-rule="evenodd" />
                                        </svg>
                                     </span>
                                </li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
        </template>
        <template v-slot:buttons>
            <button type="button" class="inline-flex w-full justify-center rounded-md bg-green-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-green-500 sm:ml-3 sm:w-auto" @click="changeLawyerAndManager">
                Применить
            </button>
            <button type="button" class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50 sm:mt-0 sm:w-auto" @click="changeLawyerAndManagerModalOptions.isOpen = false; changeLawyerAndManagerModalOptions.clickedSelect = false" ref="cancelButtonRef">
                Отмена
            </button>
        </template>
    </Modal>

    <Modal
        :open="changeDatePaymentModalOptions.isOpen"
        @close="changeDatePaymentModalOptions.isOpen = false"
    >
        <template v-slot:body>
            <div class="sm:flex sm:items-start">
                <div class="mt-3 text-center sm:mt-0 sm:text-left w-full">
                    <h3 class="text-base font-semibold leading-6 text-gray-900 mb-2">
                        Даты оплаты
                    </h3>
                    <div class="flex flex-col w-full">
                        <label for="first-date">Первая дата</label>
                        <input v-model="changeDatePaymentModalOptions.firstDate" id="first-date" type="number" placeholder="Введите дату" class="block mb-2 w-full rounded-md border-0 py-1.5 pr-22 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6 [&::-webkit-inner-spin-button]:appearance-none [&::-webkit-outer-spin-button]:m-0 [appearance:textfield]">
                        <label for="second-date">Вторая дата</label>
                        <input v-model="changeDatePaymentModalOptions.secondDate" id="second-date" type="number" placeholder="Введите дату" class="block w-full rounded-md border-0 py-1.5 pr-22 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6 [&::-webkit-inner-spin-button]:appearance-none [&::-webkit-outer-spin-button]:m-0 [appearance:textfield]">
                    </div>
                </div>
            </div>
        </template>
        <template v-slot:buttons>
            <button type="button" class="inline-flex w-full justify-center rounded-md bg-green-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-green-500 sm:ml-3 sm:w-auto" @click="changeDatePayment">
                Применить
            </button>
            <button type="button" class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50 sm:mt-0 sm:w-auto" @click="changeDatePaymentModalOptions.isOpen = false" ref="cancelButtonRef">
                Отмена
            </button>
        </template>
    </Modal>

    <Modal
        :open="changeNameModalOptions.isOpen"
        @close="changeNameModalOptions.isOpen = false"
    >
        <template v-slot:body>
            <div class="sm:flex sm:items-start">
                <div class="mt-3 text-center sm:mt-0 sm:text-left w-full">
                    <h3 class="text-base font-semibold leading-6 text-gray-900 mb-2">
                        ФИО клиента
                    </h3>
                    <div class="flex flex-col w-full">
                        <input v-model="changeNameModalOptions.text" type="text" placeholder="Введите имя" class="block w-full rounded-md border-0 py-1.5 pr-22 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6 [&::-webkit-inner-spin-button]:appearance-none [&::-webkit-outer-spin-button]:m-0 [appearance:textfield]">
                    </div>
                </div>
            </div>
        </template>
        <template v-slot:buttons>
            <button type="button" class="inline-flex w-full justify-center rounded-md bg-green-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-green-500 sm:ml-3 sm:w-auto" @click="changeName">
                Применить
            </button>
            <button type="button" class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50 sm:mt-0 sm:w-auto" @click="changeNameModalOptions.isOpen = false" ref="cancelButtonRef">
                Отмена
            </button>
        </template>
    </Modal>

    <Modal
        :open="allPaymentsModalOptions.isOpen"
        @close="allPaymentsModalOptions.isOpen = false"
    >
        <template v-slot:body>
            <div class="sm:flex sm:items-start">
                <div class="mt-3 text-center sm:mt-0 sm:text-left w-full">
                    <h3 class="text-base font-semibold leading-6 text-gray-900 mb-2">
                        Платежи
                    </h3>
                    <div class="flex flex-col w-full h-full">
                        <ul class="mt-1 w-full overflow-auto rounded-md bg-white py-1 text-base shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none sm:text-sm" tabindex="-1" role="listbox" aria-labelledby="listbox-label" aria-activedescendant="listbox-option-3">
                            <li v-for="(payment, index) in allPaymentsModalOptions.payments" :key="'payment_option-'+index" class="text-gray-900 hover:text-gray-950 cursor-pointer relative select-none py-2 pl-3 pr-9" id="listbox-option-0" role="option">
                                <div class="flex items-center">
                                    <span class="font-normal ml-3 block truncate">{{payment.amount}} {{' - '}} {{formatDate(new Date(payment.payed_at))}}</span>
                                </div>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </template>
        <template v-slot:buttons>
            <button type="button" class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50 sm:mt-0 sm:w-auto" @click="allPaymentsModalOptions.isOpen = false" ref="cancelButtonRef">
                Закрыть
            </button>
        </template>
    </Modal>
</template>

<script setup>

    import FilterSelect from "../components/FilterSelect.vue";
    import Pagination from "../components/Pagination.vue";
    import InputFilter from "../components/InputFilter.vue";
    import CalendarFilter from "../components/CalendarFilter.vue"
    import Modal from "../components/Modal.vue";
    import {ref} from "vue";
    import tr from "air-datepicker/locale/tr";
    import {Inertia} from "@inertiajs/inertia";
    import formatDate from "../utils/formatDate";
    import {typeOf} from "uri-js/dist/esnext/util";

    const props = defineProps([
        'payPlanItem',
        'clients',
        'offices',
        'managers',
        'lawyers',
        'filters',
        'role'
       ])

    const allPaymentsModalOptions = ref({
        isOpen: false,
        payments: null
    })

    const changeNameModalOptions = ref({
        isOpen: false,
        text: null,
        id: null
    })

    const changeDatePaymentModalOptions = ref({
        isOpen: false,
        firstDate: null,
        secondDate: null,
        id: null
    })

    const changeLawyerAndManagerModalOptions = ref({
        isOpen: false,
        employee_id: null,
        employee_type: null,
        title: null,
        clickedSelect: false,
        id: null
    })

    function openAllPaymentsModal(payments){
        if (payments.length > 0) {
            allPaymentsModalOptions.value.payments = payments
            allPaymentsModalOptions.value.isOpen = true
        }
    }

    function openChangeNameModal(id, text){
        changeNameModalOptions.value.id = id
        changeNameModalOptions.value.text = text
        changeNameModalOptions.value.isOpen = true
    }

    function openChangeDatePaymentModal(id, firstDate, secondDate){
        if (props.role !== 'Lawyer') {
            return
        }

        changeDatePaymentModalOptions.value.id = id
        changeDatePaymentModalOptions.value.firstDate = firstDate
        changeDatePaymentModalOptions.value.secondDate = secondDate
        changeDatePaymentModalOptions.value.isOpen = true
    }

    function openChangeLawyerAndManagerModal(id, employee_type, title, employee_id){
        if (props.role !== 'Lawyer' && employee_type === 'manager') {
            return
        }

        console.log(employee_id);
        console.log(props.lawyers[employee_id]);

        changeLawyerAndManagerModalOptions.value.id = id
        changeLawyerAndManagerModalOptions.value.title = title
        changeLawyerAndManagerModalOptions.value.employee_id = employee_id
        changeLawyerAndManagerModalOptions.value.employee_type = employee_type
        changeLawyerAndManagerModalOptions.value.isOpen = true
    }


    function changeName() {
        if (changeNameModalOptions.value.text === '') {
            return
        }

        Inertia.post('/update-data', {
            id: changeNameModalOptions.value.id,
            name: changeNameModalOptions.value.text
        })

        changeNameModalOptions.value.isOpen = false
    }

    function changeDatePayment() {
        if (changeDatePaymentModalOptions.value.firstDate === null || changeDatePaymentModalOptions.value.secondDate === null) {
            return
        }

        Inertia.post('/update-data', {
            id: changeDatePaymentModalOptions.value.id,
            first_date: changeDatePaymentModalOptions.value.firstDate,
            second_date: changeDatePaymentModalOptions.value.secondDate
        })

        changeDatePaymentModalOptions.value.isOpen = false
    }

    function changeLawyerAndManager() {
        console.log(changeLawyerAndManagerModalOptions.value.employee_type);
        console.log(changeLawyerAndManagerModalOptions.value.employee_id);

        if (changeLawyerAndManagerModalOptions.value.employee_type === 'manager') {
            Inertia.post('/update-data', {
                id: changeLawyerAndManagerModalOptions.value.id,
                manager_id: changeLawyerAndManagerModalOptions.value.employee_id,
            })
        }
        else {
            Inertia.post('/update-data', {
                id: changeLawyerAndManagerModalOptions.value.id,
                lawyer_id: changeLawyerAndManagerModalOptions.value.employee_id,
            })
        }

        changeLawyerAndManagerModalOptions.value.clickedSelect = false
        changeLawyerAndManagerModalOptions.value.isOpen = false
    }

</script>

