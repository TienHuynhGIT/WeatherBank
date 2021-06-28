<template>
    <div id="app">
        <Header></Header>
        <b-form-input class="center-content search-bar fs-5"
                      placeholder="Search..." type="text"
                      v-if="homePage" v-on:keyup.enter="fetchWeather('Enter')"
                      v-model="city" :state="fetchState"/>
        <router-view class="margin-main" @toggleView="toggleView" @toggleUnits="toggleUnits" :view="view"
                     :weather_current="weather_current"
                     :weather_forecast="weather_forecast" :units_metric="units_metric"/>
        <Footer></Footer>
    </div>
</template>

<script>
    import Header from './components/Header'
    import Footer from './components/Footer'

    export default {
        name: "App",
        components: {
            Header,
            Footer,
        },
        data() {
            return {
                api_key: "c0b8ce385b95897213f4c4b75e350e06",
                url_base: "https://api.openweathermap.org/data/2.5/",
                url_hour: "https://api.openweathermap.org/data/2.5/onecall?lat=",
                units: "metric",
                exclude: "current,minutely",
                city: "",
                weather_current: [],
                weather_forecast: [],
                view: "",
                fetchState: null,
                units_metric: true,
            }
        },
        methods: {
            async fetchWeather(key) {
                if (key == "Enter") {
                    this.view = "current"
                }

                try {
                    let data_current = await fetch(this.url_base + "weather?q=" + this.city + "&units="
                        + this.units + "&appid=" + this.api_key)
                    if (data_current.ok) {
                        this.fetchState = true
                    } else {
                        this.fetchState = false
                    }
                    this.weather_current = await data_current.json()
                } catch (e) {
                    alert(e);
                }

                if (this.fetchState) {
                    try {
                        let data_forecast = await fetch(this.url_hour + this.weather_current.coord.lat + "&lon="
                            + this.weather_current.coord.lon + "&exclude=" + this.exclude + "&appid=" + this.api_key
                            + "&units=" + this.units)
                        this.weather_forecast = await data_forecast.json()
                    } catch (e) {
                        alert(e);
                    }
                }
            },
            toggleView(view) {
                this.view = view
            },
            toggleUnits() {
                this.units_metric = !this.units_metric
                if (this.units_metric) {
                    this.units = "metric"
                } else {
                    this.units = "imperial"
                }
                this.fetchWeather()
            },
        },
         computed: {
            homePage() {
                if (this.$route.path == "/") {
                    return true
                } else {
                    return false
                }
            }
        },
    }
</script>

<style>
    #app {
        text-align: center;
    }

    .center-content {
        margin-right: auto;
        margin-left: auto;
    }

    .margin-main {
        margin-top: 20px;
    }

    .search-bar {
        max-width: 10%;
    }
</style>
