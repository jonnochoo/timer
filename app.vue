<template>
    <div class="container mx-auto">
        <div class="m-8 flex justify-center">
            <button class="btn btn-primary" @click="timer.start(0, 60)">
                60 minutes
            </button>
            <button class="btn btn-primary" @click="timer.start(0, 40)">
                40 minutes
            </button>
            <button class="btn btn-primary" @click="timer.start(0, 20)">
                20 minutes
            </button>
            <button class="btn btn-primary" @click="timer.start(0, 10)">
                10 minutes
            </button>
            <button class="btn btn-primary" @click="timer.start(0, 5)">
                5 minutes
            </button>
            <button class="btn btn-primary" @click="timer.start(0, 0, 5)">
                5 seconds
            </button>
        </div>

        <div v-if="timer.isStarted" class="flex justify-center font-extralight">
            <div class="text-8xl">{{ timer.fullDisplay }}</div>
        </div>

        <div v-if="timer.isStarted" class="mt-4 flex justify-center">
            <button
                v-if="timer.isRunning"
                @click="timer.pause"
                class="btn btn-secondary"
            >
                Pause
            </button>
            <button v-else @click="timer.resume" class="btn btn-secondary">
                Resume
            </button>
            <button @click="timer.stop" class="btn btn-secondary">Stop</button>
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
    fullDisplay: '',
    setIntervalId: null as Timeout,
    isRunning: false,
    isStarted: false,
    start: function (hours: number, minutes: number, seconds: number = 0) {
        this.audio = new Audio()
        this.audio.src = 'service-bell-ring-14610.mp3'
        this.audio.preload = 'auto'

        this.stop()

        this.isRunning = true
        this.isStarted = true
        this.hours = hours
        this.minutes = minutes
        this.seconds = seconds
        this.setIntervalId = setInterval(() => {
            this.update()
        }, 1000)
        this.formatDisplay(this.hours, this.minutes, this.seconds)
    },
    stop: function () {
        this.pause()
        this.isStarted = false
        this.recordedSeconds = 0
        this.hours = 0
        this.minutes = 0
        this.seconds = 0
        this.display = '--:--:--'
        this.fullDisplay = ''
    },
    pause: function () {
        clearInterval(this.setIntervalId)
        this.isRunning = false
    },
    resume: function () {
        this.isRunning = true
        setTimeout(() => {
            this.setIntervalId = setInterval(() => {
                this.update()
            }, 1000)
        })
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
        const hour = hours ?? 0
        const min = minutes ?? 0
        const sec = seconds ?? 0
        this.display = `${hour}:${min}:${sec}`
        if (
            (hours == 0 || hours == null) &&
            (minutes == 0 || minutes == null)
        ) {
            this.fullDisplay = `${sec} seconds`
        } else if (hours == 0 || hours == null) {
            this.fullDisplay = `${min} minutes ${sec} seconds`
        } else {
            this.fullDisplay = `${hour} hours ${min} minutes ${sec} seconds`
        }
    },
})

useHead({
    title: computed(() => timer.fullDisplay),
})
</script>
