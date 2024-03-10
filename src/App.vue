<template>
    <div class="wrapper">
        <div class="left-block">
            <h1>Погодное приложение</h1>
            <p>Узнать погоду в {{ city == "" ? "вашем городе" : getCity }}</p>
            <!-- <input type="text" v-on:input="this.city = $event.target.value" placeholder="Введите название города"> -->
            <input type="text" v-model="city" placeholder="Введите название города">
            <button v-if="city != ''" @click="getWeather()">Узнать погоду</button>
            <button disabled v-else>Введите название города</button>
            <p class="error" v-if="error != ''">{{ error }}</p>
            <div v-if="info != null">
                <p>{{ showTemp }}</p>
                <p>{{ showFeelsLike }}</p>
                <p>{{ showMinTemp }}</p>
                <p>{{ showMaxTemp }}</p>
                <p>{{ showHumidity }}</p>
                <p>{{ showPressure }}</p>
            </div>
        </div>
        <div class="right-block">
            <label for="units">Единицы измерения:</label>
            <select name="units" id="units" v-model="selectedValue">
                <option v-for="option in options" :key="option.id" :value="option.value">{{ option.label }}</option>
            </select>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import { VITE_APP_API_KEY } from '../.env'

export default {
    data() {
        return {
            city: "",
            error: "",
            info: null,
            options: [
                { value: "metric", label: "Цельсий (С)" },
                { value: "imperial", label: "Фаренгейт (F)" }
            ],
            selectedValue: "metric",
        }
    },
    computed: {
        getCity() {
            return this.city;
        },
        showTemp() {
            return "Температура: " + this.info.main.temp;
        },
        showFeelsLike() {
            return "Ощущается как: " + this.info.main.feels_like;
        },
        showMinTemp() {
            return "Минимальная температура: " + this.info.main.temp_min;
        },
        showMaxTemp() {
            return "Максимальная температура: " + this.info.main.temp_max;
        },
        showHumidity() {
            return "Влажность: " + this.info.main.humidity;
        },
        showPressure() {
            return "Давление: " + this.info.main.pressure;
        },
    },
    methods: {
        getWeather() {
            if (this.city.trim().length < 2) {
                this.error = "Нужно название более одного сивола"
                this.info = null
                return false
            }
            this.error = ""
            axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=${this.selectedValue}&appid=${VITE_APP_API_KEY}`)
                .then(response => (this.info = response.data))
                .catch(error => {
                    this.error = "Город не найден"
                    this.info = null
                })
        }
    }
}

</script>

<style scoped>
.error {
    color: red;
    font-size: 12px;
    margin-top: 10px;
}


.wrapper {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    width: 750px;
    height: 500px;
    border-radius: 50px;
    padding: 20px;
    background: #101010;
    color: #fff;
}

.wrapper .left-block .right-block {
    margin-top: 50px;
    margin-left: 50px;
    width: 100%;
    vertical-align: bottom;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

.wrapper h1 {
    font-size: 30px;
    margin-top: 40px 0;
}

.wrapper p {
    font-size: 20px;
    margin-top: 20px;
}

.wrapper input {
    margin-top: 30px;
    margin-bottom: 10px;
    background: transparent;
    border: 0;
    border-bottom: 2px solid #282629;
    color: #fcfcfc;
    font-size: 14px;
    padding: 5px 8px;
    text-align: left;
    outline: none;
    width: 200px;
}

.wrapper input:focus {
    border-bottom-color: #6e2d7d;

}

.wrapper button {
    color: #fff;
    background: #e3bc4b;
    border: 2px solid #b99935;
    padding: 10px 15px;
    border-radius: 10px;
    cursor: pointer;
    display: flex;
    /* justify-content: center; */
    align-items: center;
    margin-top: 10px;
    transition: transform 500ms ease;
}

.wrapper button:disabled {
    background: #746027;
    opacity: 0.5;
    cursor: not-allowed;
}

.wrapper button:hover {
    transform: scale(1.0) translateY(-1px);
}
</style>