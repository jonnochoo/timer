<template>
    <div class="container mx-auto">
        <div class="m-8 flex justify-center">
            <button class="btn-primary" @click="timer.start(0, 60)">
                60 minutes
            </button>
            <button class="btn-primary" @click="timer.start(0, 40)">
                40 minutes
            </button>
            <button class="btn-primary" @click="timer.start(0, 20)">
                20 minutes
            </button>
            <button class="btn-primary" @click="timer.start(0, 10)">
                10 minutes
            </button>
            <button class="btn-primary" @click="timer.start(0, 5)">
                5 minutes
            </button>
            <button class="btn-primary" @click="timer.start(0, 0, 5)">
                5 seconds
            </button>
        </div>

        <div class="flex justify-center text-9xl font-extralight">
            {{ timer.display }}
        </div>

        <div class="mt-4 flex justify-center">
            <button @click="timer.stop" class="btn-primary">Stop</button>
        </div>
    </div>
</template>

<script lang="ts" setup>
import '~/assets/main.css'

import { intervalToDuration, addMinutes, addSeconds } from 'date-fns'

const timer = reactive({
    audio: null as Audio,
    hours: 0,
    minutes: 0,
    seconds: 0,
    display: '00:00:00',
    timestamp: new Date(),
    setIntervalId: null as Timeout,
    start: function (hours: number, minutes: number, seconds: number = 0) {
        this.audio = new Audio()
        this.audio.src = 'service-bell-ring-14610.mp3'
        this.audio.preload = 'auto'

        this.hours = hours
        this.minutes = minutes
        this.seconds = seconds
        this.timestamp = new Date()
        this.formatDisplay(hours, minutes, seconds)
        setTimeout(() => {
            this.setIntervalId = setInterval(() => {
                this.update()
            }, 1000)
        })
    },
    stop: function () {
        clearInterval(this.setIntervalId)
        this.hours = 0
        this.minutes = 0
        this.seconds = 0
        this.display = '--:--:--'
    },
    update: function () {
        const start = new Date()
        const end = addSeconds(
            addMinutes(timer.timestamp, this.minutes),
            this.seconds
        )
        if (start >= end) {
            this.audio.play()
            console.log('completed', this.setIntervalId)
            clearInterval(this.setIntervalId)
            return
        }
        let duration = intervalToDuration({
            end: end,
            start: start,
        })
        this.formatDisplay(duration.hours, duration.minutes, duration.seconds)
    },
    formatDisplay(hours: number, minutes: number, seconds: number) {
        const hour = (hours?.toString() ?? '00').padStart(2, '0')
        const min = (minutes?.toString() ?? '00').padStart(2, '0')
        const sec = (seconds?.toString() ?? '00').padStart(2, '0')
        this.display = `${hour}:${min}:${sec}`
    },
})

useHead({
    title: computed(() => timer.display),
})
</script>
