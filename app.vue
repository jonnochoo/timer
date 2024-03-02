<template>
    <div class="container mx-auto">
        <div class="m-8 flex justify-center">
            <button class="btn-primary" @click="timer.start(60)">
                60 minutes
            </button>
            <button class="btn-primary" @click="timer.start(40)">
                40 minutes
            </button>
            <button class="btn-primary" @click="timer.start(20)">
                20 minutes
            </button>
            <button class="btn-primary" @click="timer.start(10)">
                10 minutes
            </button>
            <button class="btn-primary" @click="timer.start(5)">
                5 minutes
            </button>
            <button class="btn-primary" @click="timer.start(0, 5)">
                5 seconds
            </button>
        </div>

        <div class="flex justify-center text-8xl font-extralight">
            {{ timer.display }}
        </div>
    </div>
</template>

<script lang="ts" setup>
import '~/assets/main.css'

import { intervalToDuration, addMinutes, addSeconds } from 'date-fns'

const timer = reactive({
    minutes: 0,
    seconds: 0,
    display: '00:00:00',
    timestamp: new Date(),
    setIntervalId: null as Timeout,
    start: function (minutes: number, seconds: number = 0) {
        this.minutes = minutes
        this.seconds = seconds
        this.timestamp = new Date()
        this.update()
        this.setIntervalId = setInterval(() => {
            this.update()
        }, 1000)
    },
    update: function () {
        const start = new Date()
        const end = addSeconds(
            addMinutes(timer.timestamp, this.minutes),
            this.seconds
        )
        if (start > end) {
            console.log('done', this.setIntervalId)
            clearInterval(this.setIntervalId)
            return
        }

        let duration = intervalToDuration({
            end: end,
            start: start,
        })
        const hour = (duration.hours?.toString() ?? '00').padStart(2, '0')
        const minutes = (duration.minutes?.toString() ?? '00').padStart(2, '0')
        const seconds = (duration.seconds?.toString() ?? '00').padStart(2, '0')
        this.display = `${hour}:${minutes}:${seconds}`
    },
})
</script>
