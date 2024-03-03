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
    recordedSeconds: 0,
    display: '00:00:00',
    setIntervalId: null as Timeout,
    start: function (hours: number, minutes: number, seconds: number = 0) {
        this.audio = new Audio()
        this.audio.src = 'service-bell-ring-14610.mp3'
        this.audio.preload = 'auto'

        this.stop()

        this.hours = hours
        this.minutes = minutes
        this.seconds = seconds
        this.formatDisplay(this.hours, this.minutes, this.seconds)
        setTimeout(() => {
            this.setIntervalId = setInterval(() => {
                this.update()
            }, 1000)
        })
    },
    stop: function () {
        clearInterval(this.setIntervalId)
        this.recordedSeconds = 0
        this.hours = 0
        this.minutes = 0
        this.seconds = 0
        this.display = '--:--:--'
    },
    update: function () {
        this.recordedSeconds++
        var currentDate = new Date()
        const start = addSeconds(currentDate, this.recordedSeconds)
        const end = addSeconds(
            currentDate,
            this.seconds + this.minutes * 60 + this.hours * 60
        )

        // Play the audio and clear the interval
        if (start > end) {
            this.audio.play()
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
