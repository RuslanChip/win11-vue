<template>
    <div class="calendar flex flex-col bg-white bg-opacity-80 dark:bg-gray-600 dark:bg-opacity-50 backdrop-filter backdrop-blur-lg rounded-md shadow-md duration-200 overflow-hidden" :class="{ 'show': isOpen }">
        <div class="flex items-center bg-black bg-opacity-5 px-6 py-4">
            <span class="text-sm text-gray-700 dark:text-white flex-grow">{{ currentDate.format('dddd, MMMM D') }}</span>
            <button class="bg-gray-400 bg-opacity-10 border border-gray-400 border-opacity-20 p-1.5 rounded-md">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512" style="width: 10px; height: 10px;" class="transform rotate-180 fill-current text-gray-700 dark:text-gray-200">
                    <path d="M240.971 130.524l194.343 194.343c9.373 9.373 9.373 24.569 0 33.941l-22.667 22.667c-9.357 9.357-24.522 9.375-33.901.04L224 227.495 69.255 381.516c-9.379 9.335-24.544 9.317-33.901-.04l-22.667-22.667c-9.373-9.373-9.373-24.569 0-33.941L207.03 130.525c9.372-9.373 24.568-9.373 33.941-.001z"></path>
                </svg>
            </button>
        </div>
        <div class="flex justify-center items-center gap-3 px-6 pt-4">
            <span class="text-gray-700 dark:text-white text-sm flex-grow">{{ this.date.format('MMMM') + ' ' + this.date.format('YYYY') }}</span>
            <button class="flex justify-center items-center hover:bg-gray-600 hover:bg-opacity-10 dark:hover:bg-white dark:hover:bg-opacity-10 rounded-md duration-150" @click="addMonth()">
                <svg class="fill-current text-gray-600 dark:text-gray-400 p-2" width="24" height="24" viewBox="0 0 27 23" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <path d="M11.7679 0.999999C12.5378 -0.333335 14.4623 -0.333333 15.2321 1L26.0574 19.75C26.8272 21.0833 25.8649 22.75 24.3253 22.75H2.67468C1.13508 22.75 0.172831 21.0833 0.942632 19.75L11.7679 0.999999Z"/>
                </svg>
            </button>
            <button class="flex justify-center items-center hover:bg-gray-600 hover:bg-opacity-10 dark:hover:bg-white dark:hover:bg-opacity-10 rounded-md duration-150" @click="subtractMonth()">
                <svg class="fill-current text-gray-600 dark:text-gray-400 p-2 transform rotate-180" width="24" height="24" viewBox="0 0 27 23" xmlns="http://www.w3.org/2000/svg">
                    <path d="M11.7679 0.999999C12.5378 -0.333335 14.4623 -0.333333 15.2321 1L26.0574 19.75C26.8272 21.0833 25.8649 22.75 24.3253 22.75H2.67468C1.13508 22.75 0.172831 21.0833 0.942632 19.75L11.7679 0.999999Z"/>
                </svg>
            </button>
        </div>
        <div class="flex-grow grid items-center grid-rows-6 grid-cols-7 p-4 gap-2">
            <div v-for="(day, index) in weekDays" :key="index" class="flex justify-center items-center text-sm text-gray-600 dark:text-gray-100">
                {{ day }}
            </div>
            <div v-for="(day, index) in days" :key="index" class="flex justify-center items-center text-sm rounded-full w-8 h-8 duration-150" :class="{ 'text-gray-600 hover:bg-gray-600 hover:bg-opacity-10 dark:hover:bg-white dark:hover:bg-opacity-10 dark:text-gray-300': day.state === 0, 'text-gray-400 dark:text-gray-500': day.state !== 0  }">
                {{ day.day }}
            </div>
        </div>
    </div>
</template>

<script>
import moment from 'moment'

export default {
    name: 'Calendar',
    computed: {
        isOpen () {
            return this.$store.state.calendarOpen
        }
    },
    data() {
        return {
            weekDays: ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa'],
            currentDate: moment(),
            date: moment(),
            days: []
        }
    },
    methods: {
        addMonth() {
            this.date.add('1', 'months')
            this.updateTime()
        },
        subtractMonth() {
            this.date.subtract('1', 'months')
            this.updateTime()
        },
        updateTime() {

            const firstDay = this.date.startOf('month').day()
            const daysNumber = this.date.daysInMonth()

            let daysObject = {}

            for (let i = 0; i < 42; i++) {
                if (i < firstDay) {
                    daysObject[i] = {
                        day: this.date.clone().subtract((firstDay - i), 'days').format('D'),
                        state: -1
                    }
                } else if (i < (daysNumber + firstDay)) {
                    daysObject[i] = {
                        day: i - firstDay + 1,
                        state: 0
                    }
                } else {
                    daysObject[i] = {
                        day: this.date.clone().add((i - daysNumber - firstDay), 'days').format('D'),
                        state: 1
                    }
                }
            }

            this.days = daysObject

        }
    },
    mounted() {
        this.updateTime()
    }
}
</script>

<style lang="scss" scoped>

.calendar {
    position: absolute !important;
    right: 10px;
    bottom: -100px;
    width: 290px;
    height: 400px;
    transform: scale(0.9);
    opacity: 0;
}

.calendar.show {
    bottom: 50px;
    transform: scale(1);
    opacity: 1;
}

</style>