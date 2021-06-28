<template>
    <div>
        <div class="center-content align-content rounded bg-light shadow-sm border"
             v-if="view=='current'">
            <b-row>
                <b-col>Felt Temperature</b-col>
                <b-col>{{ Math.round(weather_current.main.feels_like) }} {{ unitsTemp() }}</b-col>
                <b-col>Wind</b-col>
                <b-col>
                    {{ windDirection(weather_current.wind.deg) }} /
                    {{ unitsSpeed(weather_current.wind.speed) }}
                </b-col>
            </b-row>
            <b-row>
                <b-col>Min/Max</b-col>
                <b-col>
                    {{ Math.round(weather_current.main.temp_min) }} /
                    {{Math.round(weather_current.main.temp_max) }} {{ unitsTemp() }}
                </b-col>
                <b-col>Cloudiness</b-col>
                <b-col>{{ weather_current.clouds.all}} %</b-col>
            </b-row>
            <b-row>
                <b-col> Humidity</b-col>
                <b-col>{{ weather_current.main.humidity }} %</b-col>
                <b-col>Visibility</b-col>
                <b-col>{{ unitsLength(weather_current.visibility) }}</b-col>
            </b-row>
            <b-row>
                <b-col>Atmos. Pressure</b-col>
                <b-col>{{ weather_current.main.pressure }} hPa</b-col>
                <b-col>Sunrise/Sunset</b-col>
                <b-col>
                    {{ dateBuilder(weather_current.sys.sunrise) }} /
                    {{dateBuilder(weather_current.sys.sunset) }}
                </b-col>
            </b-row>
        </div>
        <div class="w-50 center-content align-content rounded-bottom shadow-sm border"
             v-if="view=='hourly'">
            <b-row>
                <b-col>Felt Temperature</b-col>
                <b-col>{{ Math.round(weather_forecast.feels_like) }} {{ unitsTemp() }}</b-col>
                <b-col>Wind</b-col>
                <b-col>{{ windDirection(weather_forecast.wind_deg) }} / {{
                    unitsSpeed(weather_forecast.wind_speed) }}
                </b-col>
            </b-row>
            <b-row>
                <b-col>Humidity</b-col>
                <b-col>{{ weather_forecast.humidity }} %</b-col>
                <b-col>Cloudiness</b-col>
                <b-col>{{ weather_forecast.clouds }} %</b-col>
            </b-row>
            <b-row>
                <b-col>Atmos. Pressure</b-col>
                <b-col>{{ weather_forecast.pressure }} hPa</b-col>
                <b-col>Visibility</b-col>
                <b-col>{{ unitsLength(weather_forecast.visibility) }}</b-col>
            </b-row>
            <b-row>
                <b-col class="inline-row">Atmos. Temperature</b-col>
                <b-col>{{ Math.round(weather_forecast.dew_point) }} {{ unitsTemp() }}</b-col>
                <b-col>UV Index</b-col>
                <b-col>{{ Math.round(weather_forecast.uvi) }}</b-col>
            </b-row>
        </div>
        <div class="w-50 center-content align-content rounded-bottom shadow-sm border"
             v-if="view=='daily'">
            <b-row>
                <b-col>Humidity</b-col>
                <b-col>{{ weather_forecast.humidity }} %</b-col>
                <b-col>UV Index</b-col>
                <b-col>{{ Math.round(weather_forecast.uvi) }}</b-col>
            </b-row>
            <b-row>
                <b-col>Atmos. Pressure</b-col>
                <b-col>{{ weather_forecast.pressure }} hPa</b-col>
                <b-col>Sunrise/Sunset</b-col>
                <b-col>{{ dateBuilder(weather_forecast.sunrise) }} / {{
                    dateBuilder(weather_forecast.sunset) }}
                </b-col>
            </b-row>
            <b-row>
                <b-col>Wind</b-col>
                <b-col>
                    {{ windDirection(weather_forecast.wind_deg) }} / {{
                    unitsSpeed(weather_forecast.wind_speed) }}
                </b-col>
                <b-col>Moonrise/Moonset</b-col>
                <b-col>{{ dateBuilder(weather_forecast.moonrise) }} / {{
                    dateBuilder(weather_forecast.moonset) }}
                </b-col>
            </b-row>
            <b-row>
                <b-col>Cloudiness</b-col>
                <b-col>{{ weather_forecast.clouds }} %</b-col>
                <b-col>Moon Phase</b-col>
                <b-col>{{ moonPhase(weather_forecast.moon_phase) }}</b-col>
            </b-row>
        </div>
    </div>
</template>

<script>
    export default {
        name: "Details",
        props: {
            weather_current: Object,
            weather_forecast: Object,
            view: String,
            weather_timezone: String,
            units_metric: Boolean,
        },
        methods: {
            dateBuilder(time) {
                let options = {timeZone: this.weather_timezone, hour: "numeric", minute: "2-digit"}
                return new Date(time * 1000).toLocaleString([], options)
            },
            windDirection(degrees) {
                let wind_directions = {
                    1: "N", 2: "NNE", 3: "NE", 4: "ENE", 5: "E", 6: "ESE", 7: "SE", 8: "SSE", 9: "S", 10: "SSW",
                    11: "SW", 12: "WSW", 13: "W", 14: "WNW", 15: "NW", 16: "NNW", 17: "N"
                }
                return wind_directions[Math.round(degrees % 360 / 22.5) + 1]
            },
            moonPhase(phase) {
                let moon_phase = {
                    0: "new moon",
                    1: "new moon",
                    0.25: "first quarter moon",
                    0.5: "full moon",
                    0.75: "last quarter moon"
                }
                if ((phase == 0) || (phase == 1) || (phase == 0.25) || (phase == 0.5) || (phase == 0.75)) {
                    return moon_phase[phase]
                } else if (phase < 0.25) {
                    return "waxing crescent"
                } else if (phase < 0.5) {
                    return "waxing gibous"
                } else if (phase < 0.75) {
                    return "waning gibous"
                } else if (phase < 1) {
                    return "waning crescent"
                }
            },
            unitsTemp() {
                if (this.units_metric) {
                    return "°C"
                } else {
                    return "°F"
                }
            },
            unitsSpeed(speed) {
                if (this.units_metric) {
                    return Math.round(speed * 3.6) + " km/h"
                } else {
                    return Math.round(speed) + " mph"
                }
            },
            unitsLength(meter) {
                if (this.units_metric) {
                    return Math.round(meter / 1000) + " km"
                } else {
                    return Math.round(meter / 1609) + " mile(s)"
                }
            },
        }
    }
</script>

<style scoped>
    .align-content {
        text-align: left;
        padding-left: 20px;
        padding-top: 10px;
        padding-bottom: 10px;
    }

    .inline-row {
        white-space: nowrap;
    }

</style>
