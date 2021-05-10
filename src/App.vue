<template>
    <div id="app">
        <main class="container">
            <form class="search-form" v-on:submit="getTemperature">
                <input
                    type="text"
                    class="search-bar"
                    v-model="place"
                    placeholder="search..."
                />
                <button type="submit">Search</button>
            </form>
            <div class="display" v-if="loaded">
                <h2 class="name">
                    Weather in {{ weather.name }} ({{weather.sys.country}})
                </h2>
                <div class="row temperature">
                    Temperature: {{Math.round(weather.main.temp) + " &#186;C"}}
                </div>
                <div class="row sun">
                  Sunrise: {{new Date((weather.sys.sunrise + weather.timezone - 7200)*1000).toLocaleTimeString()}}
                  <br />
                  Sunset: {{new Date((weather.sys.sunset + weather.timezone - 7200)*1000).toLocaleTimeString()}}
                </div>
                <div class="row description">
                  Sky: {{weather.weather[0].description}}
                </div>
                <div style="width: 100%"><iframe width="100%" height="400" frameborder="0" scrolling="no" marginheight="0" marginwidth="0" v-bind:src="`https://maps.google.com/maps?width=100%25&amp;height=600&amp;hl=en&amp;q=` + weather.name + `+()&amp;t=&amp;z=6&amp;ie=UTF8&amp;iwloc=B&amp;output=embed`"></iframe><a href="https://www.maps.ie/draw-radius-circle-map/">Google radius map</a></div>
            </div>
        </main>
    </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
    name: "App",
    data() {
        return {
            api_key: "20c65fd68a54a621a2fbcc82aab37b17",
            weather: {},
            place: "",
            loaded: false,
        };
    },
    methods: {
        getTemperature: async function (e: Event) {
            e.preventDefault();
            const response = await fetch(
                `https://api.openweathermap.org/data/2.5/weather?q=${this.place}&units=metric&appid=${this.api_key}`
            );
            if (!response.ok) {
                this.logError(response);
                return;
            }
            const json = await response.json();
            this.setResults(json);
        },
        setResults(results: any) {
            this.weather = results;
            this.loaded = true;
            console.log(results);
        },
        logError: function (response: Response) {
          const status = response.status;
            if (status === 404) console.error(status, "Couldn't find a place from the query")
            if (status === 401) console.error(status, "Your API key is incorrect")
        },
    },
});
</script>

<style lang="scss">
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}
body {
    font-family: "montserrat", sans-serif;
    transition: 0.4s;
    // #app {
    // }

    .container {
        width: 800px;
        max-width: 100%;
        margin: 0 auto;
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .display {
      display: flex;
      flex-direction: column;
      align-items: center;

      .name {
        margin-top: 20px;
        margin-bottom: 10px;
      }

      .row {
        margin: 10px 0;
      }
    }
}

main {
    min-height: 100vh;
    padding: 50px 0;
}
</style>
