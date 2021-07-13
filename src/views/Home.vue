<template>
    <h2 v-if="view==''">A accurate, fast and simple weather app.</h2>
    <div v-else>
        <b-button-group class="button-view">
            <b-button class="shadow-sm" @click="$emit('toggleView', 'current')">Current</b-button>
            <b-button class="shadow-sm" @click="$emit('toggleView', 'hourly')">Hourly</b-button>
            <b-button class="shadow-sm" @click="$emit('toggleView', 'daily')">Daily</b-button>
            <b-button class="shadow-sm" v-if="units_metric" @click="$emit('toggleUnits')">
                Metric
                <b-icon-toggle-on></b-icon-toggle-on>
            </b-button>
            <b-button class="shadow-sm" v-else @click="$emit('toggleUnits')">
                Metric
                <b-icon-toggle-off></b-icon-toggle-off>
            </b-button>
        </b-button-group>
        <b-container class="main-header">
            <b-row class="fs-5">{{ view[0].toLocaleUpperCase() + view.slice(1)}} Weather</b-row>
            <b-row>{{ weather_current.name }}, {{ weather_current.sys.country }}</b-row>
            <b-row v-if="view=='current'">{{ dateBuilder(weather_current.dt) }}</b-row>
        </b-container>
        <b-container>
            <div v-if="view=='current'">
                <b-container class="warning-text shadow-sm rounded border"
                             v-if="weather_forecast.hasOwnProperty('alerts')">
                    <div class="fs-4">{{weather_forecast.alerts[0].sender_name}},</div>
                    <div class="fs-4">{{weather_forecast.alerts[0].event}}</div>
                </b-container>
            </div>
            <div v-if="view=='current'">
                <Weathers :view="view" :weather_current="weather_current" :weather_timezone="weather_forecast.timezone"
                          :units_metric="units_metric"></Weathers>
            </div>
            <div v-if="view=='hourly'">
                <div :key="weather.dt"
                     v-for="weather in this.weather_forecast.hourly.slice(1, calcRemainHours(weather_forecast.hourly[1].dt) + 1)">
                    <Weathers :view="view" :weather_forecast="weather" :weather_timezone="weather_forecast.timezone"
                              :units_metric="units_metric"></Weathers>
                </div>
            </div>
            <div v-if="view=='daily'">
                <b-container class="daily-header">
                    <b-row>
                        {{ dateBuilder(weather_forecast.daily[1].dt).slice(0, 7) }} -
                        {{dateBuilder(weather_forecast.daily[weather_forecast.daily.length - 1].dt).slice(0, 7) }}
                    </b-row>
                </b-container>
                <div :key="weather.dt" v-for="weather in this.weather_forecast.daily.slice(1)">
                    <Weathers :view="view" :weather_forecast="weather" :weather_timezone="weather_forecast.timezone"
                              :units_metric="units_metric"></Weathers>
                </div>
            </div>
        </b-container>
    </div>
</template>

<script>
    import Weathers from "../components/Weathers";

    export default {
        name: "Body",
        components: {
            Weathers,
        },
        data() {
            return {
                weather_timezone: "",
            }
        },
        props: {
            view: String,
            weather_current: Object,
            weather_forecast: Object,
            units_metric: Boolean,
        },
        methods: {
            dateBuilder(time) {
                let options = {
                    timeZone: this.weather_forecast.timezone,
                    year: "2-digit",
                    month: "numeric",
                    day: "numeric",
                    hour: "numeric",
                }
                if (this.view == 'current') {
                    options["minute"] = "2-digit"
                } else {
                    options["minute"] = "numeric"
                }
                return new Date(time * 1000).toLocaleString([], options)
            },
            calcRemainHours(time) {
                let hour = this.dateBuilder(time)
                return 24 - (hour[9] + hour[10])
            }
        }
    }
</script>

<style scoped>
    .main-header {
        width: 32%;
        padding-top: 10px;
    }

    .button-view {
        width: 20%;
    }

    .daily-header {
        width: 47%;
    }

    .warning-text {
        width: 35%;
        margin-top: 10px;
        margin-bottom: 5px;
        padding-top: 10px;
        padding-bottom: 10px;
        pointer-events: none;
        background-color: lightcoral;
        color: white;
    }

</style>
