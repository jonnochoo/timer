<template>
    <div class="container mx-auto">
        <div class="m-8 flex justify-center">
            <button class="btn-primary" @click="startTimer(60)">
                60 minutes
            </button>
            <button class="btn-primary" @click="startTimer(40)">
                40 minutes
            </button>
            <button class="btn-primary" @click="startTimer(20)">
                20 minutes
            </button>
            <button class="btn-primary" @click="startTimer(10)">
                10 minutes
            </button>
            <button class="btn-primary" @click="startTimer(5)">
                5 minutes
            </button>
        </div>

        <div class="flex justify-center text-8xl font-extralight">
            {{ timer.display }}
        </div>
    </div>
</template>

<script lang="ts" setup>
import '~/assets/main.css'

import { intervalToDuration, addMinutes } from 'date-fns'

const timer = reactive({
    minutes: 0,
    display: '',
    timestamp: new Date(),
    set: function (min: number) {
        this.minutes = min
        this.timestamp = new Date()
    },
    update: function () {
        let duration = intervalToDuration({
            end: addMinutes(timer.timestamp, this.minutes),
            start: new Date(),
        })
        const hour = duration.hours ?? '00'
        const minutes = duration.minutes ?? '00'
        const seconds = duration.seconds ?? '00'
        this.display = `${hour}:${minutes}:${seconds}`
    },
})

const startTimer = (minutes: number) => {
    timer.set(minutes)
    timer.update()
    setInterval(() => {
        timer.update()
    }, 1000)
}
</script>
