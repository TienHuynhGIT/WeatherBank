<template>
    <div class="padding-main">
        <div v-if="view=='current'">
            <b-row class="center-content rounded shadow-sm margin-current border" align-v="center">
                <b-col>
                    <img :src=weatherIcon(weather_current.weather[0].icon) alt="weather icon"/>
                </b-col>
                <b-col class="fs-1">
                    {{ Math.round(weather_current.main.temp) }} {{ unitsTemp() }}
                    <div class="fs-4">
                        {{ weather_current.weather[0].description[0].toUpperCase() +
                        weather_current.weather[0].description.slice(1)}}
                    </div>
                </b-col>
            </b-row>
            <b-row class="center-content w-50">
                <Details :view="view" :weather_current="weather_current" :weather_timezone="weather_timezone"
                         :units_metric="units_metric"></Details>
            </b-row>
        </div>
        <div v-else>
            <b-row :class="[this.visible ? ['w-50', 'center-content', 'shadow-sm', 'rounded', 'bg-light', 'border'] :
            ['w-50', 'center-content', 'shadow-sm', 'rounded-top', 'bg-light', 'border']]" align-v="center">
                <b-col v-if="this.view=='hourly'">{{ dateBuilder(weather_forecast.dt) }}</b-col>
                <b-col v-else>{{ dateBuilder(weather_forecast.dt) }}</b-col>
                <b-col>
                    <img :src=weatherIcon(weather_forecast.weather[0].icon) alt="weather icon"/>
                </b-col>
                <b-col v-if="view=='hourly'">
                    <div class="fs-5 inline-row">{{ Math.round(weather_forecast.temp) }} {{ unitsTemp() }}</div>
                </b-col>
                <b-col v-else>
                    <div class="fs-5 inline-row">{{ Math.round(weather_forecast.temp.day) }}</div>
                    / {{Math.round(weather_forecast.temp.night) }} {{ unitsTemp() }}
                </b-col>
                <b-col>{{ weather_forecast.weather[0].description}}</b-col>
                <b-col>
                    <b-icon-droplet></b-icon-droplet>
                    {{ Math.round(weather_forecast.pop * 100)}} %
                </b-col>
                <b-col>
                    <b-icon-chevron-down @click="toggleVisibile" v-if="this.visible==true"
                                         v-b-toggle="`${weather_forecast.dt}`"></b-icon-chevron-down>
                    <b-icon-chevron-up @click="toggleVisibile" v-else
                                       v-b-toggle="`${weather_forecast.dt}`"></b-icon-chevron-up>
                </b-col>
            </b-row>
            <b-row>
                <b-collapse :id="`${weather_forecast.dt}`">
                    <Details :view="view" :weather_forecast="weather_forecast" :weather_timezone="weather_timezone"
                             :units_metric="units_metric"></Details>
                </b-collapse>
            </b-row>
        </div>
    </div>
</template>

<script>
    import Details from './Details'

    export default {
        name: "weather",
        components: {
            Details
        },
        data() {
            return {
                visible: true
            }

        },
        props: {
            weather_current: Object,
            weather_timezone: String,
            weather_forecast: Object,
            view: String,
            units_metric: Boolean,
        },
        methods: {
            dateBuilder(time) {
                let options = {}
                if (this.view == "hourly") {
                    options = {
                        timeZone: this.weather_timezone, year: "2-digit", month: "numeric", day: "numeric",
                        hour: "numeric", minute: "2-digit"
                    }
                } else {
                    options = {
                        timeZone: this.weather_timezone, year: "2-digit", month: "numeric", day: "numeric",
                        weekday: "long"
                    }
                }
                return new Date(time * 1000).toLocaleString([], options)
            },
            weatherIcon(icon) {
                if (this.view == 'current') {
                    return "https://openweathermap.org/img/wn/" + icon + "@4x.png"
                } else {
                    return "https://openweathermap.org/img/wn/" + icon + "@2x.png"
                }
            },
            toggleVisibile() {
                this.visible = !this.visible
            },
            unitsTemp() {
                if (this.units_metric) {
                    return "°C"
                } else {
                    return "°F"
                }
            },
        }
    }
</script>

<style scoped>
    .center-content {
        margin-right: auto;
        margin-left: auto;
        width: 34%;
    }

    .inline-row {
        display: inline;
    }

    .padding-main {
        padding-top: 10px;
    }

    .margin-current {
        margin-bottom: 20px;
    }
</style>
