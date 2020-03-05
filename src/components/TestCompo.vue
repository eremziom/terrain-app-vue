<template>  
    <div class="container-fluid cont">
        <div v-show="numberVerified == false" class="row justify-content-center" ><h2>{{testProp}}</h2></div>
        <div v-show="numberVerified == false" v-bind:class="[userNumber.length === 8 ? correct : incorrect]" class="row justify-content-center nrInput"><b-form-input v-model="userNumber" id="nrAbonenta" type="number" placeholder="Numer Abonenta"></b-form-input></div>
        <div v-show="numberVerified == false && userNumber.length === 8" class="row justify-content-center "><b-button class="nrButton" v-on:click="numberCheck">WERYFIKUJ</b-button></div>
        <div v-show="userNumber.length === 0" class="row justify-content-center ">Wpisz 8-cyfrowy numer abonenta</div>
        <div v-show="userNumber.length < 8 && userNumber.length > 0" class="row justify-content-center ">Numer posaida za mało cyfr!</div>
        <div v-show="userNumber.length > 8" class="row justify-content-center ">Numer posiada za duzo cyfr!</div>
        <div v-show="numberNotVerified === true" class="row justify-content-center ">Numer nie został poprawnie zweryfikowany</div>
        <div v-show="numberVerified == true" class="row justify-content-center ">Numer abonenta <span class="verifiedNumber">{{userNumber}}</span> jest poprawny</div>
        <div v-show="numberVerified == true && categoryChosen == false" class="row justify-content-center "><b-button class="mediaButton" v-on:click="showInputs('intFon')">INTERNET / TELEFON</b-button></div>
        <div v-show="numberVerified == true && categoryChosen == false" class="row justify-content-center "><b-button class="mediaButton" v-on:click="showInputs('tv')">TELEWIZJA</b-button></div>
        <div v-show="numberVerified == true && categoryChosen == false" class="row justify-content-center "><b-button class="mediaButton" v-on:click="showInputs('hdd')">DYSK HDD</b-button></div>
        <!-- <div v-show="info != null" class="row justify-content-center "><span class="verifiedNumber">{{info}}</span></div>
        <div v-if="info != null" class="row justify-content-center "><span>{{info.USD.rate_float}}</span></div> -->
        <div v-show="devices.tv || devices.intFon || devices.hdd == true" class="row justify-content-center nrInput"><b-form-input id="ident1" placeholder="ID Urządzenia"></b-form-input></div>
        <div v-show="devices.tv == true" class="row justify-content-center nrInput"><b-form-input id="ident2" placeholder="ID Drugiego Urządzenia"></b-form-input></div>
        <div v-show="devices.tv || devices.intFon || devices.hdd == true" class="row justify-content-center "><b-button class="nrButton" >WERYFIKUJ</b-button></div>
        <div v-show="devices.tv || devices.intFon || devices.hdd == true" class="row justify-content-center "><b-button class="backButton" v-on:click="goBack">WRÓĆ</b-button></div>
    </div>
</template>

<script>
// import axios from 'axios';

export default {
    name: 'TestCompo',
    props: {
        testProp: String
    },
    methods: {
        numberCheck: function(){
            event.preventDefault();
            if(this.userNumber === this.userNumber){
                this.numberVerified = true;
            }  else {
                this.numberNotVerified = true;
            }
        },
        showInputs: function(device){
            this.devices[device] = true;
            this.categoryChosen = true;
        },
        goBack: function(){
            this.devices.tv = false;
            this.devices.intFon = false;
            this.devices.hdd = false;
            this.categoryChosen = false;
        }
        // postToApi: function(){
        //     axios
        //         .get('https://api.coindesk.com/v1/bpi/currentprice.json')
        //         .then(response => (this.info = response.data.bpi))
        //         .catch(e => {
        //             this.error = e
        //         })
        // }
    },
    data: function() {
        return {
            userNumber: '',
            correct: 'correct',
            incorrect: 'incorrect',
            numberVerified: false,
            numberNotVerified: false,
            categoryChosen: false,
            //info: null,
            devices: {
                tv: false,
                intFon: false,
                hdd: false
            },
        }
    }
}
</script>

<style lang="scss" scoped>
    .nrInput{
        width: 90%;
        margin: 10px auto;
        border: solid;
        border-width: 7px;
        border-radius: 10px;
        input {
            text-align: center;
            font-size: 30px;
        }
    }
    .nrButton {
        height: 100px;
        width: 90%;
        font-size: 40px;
        background-color: green;
        border-radius: 10px;
        border: none;
        &:hover{
            background-color: rgb(101, 168, 0);
        }
        &:focus{
            background-color: green;
            border: none;
            box-shadow: none;
        }
        &:active{
            background-color: green;
            border: none;
        }
    }
    // .btn-secondary:not(:disabled):not(.disabled):active {
    //     background-color: rgb(10, 78, 10);
    //     border: none;
    // }
    .mediaButton {
        height: 150px;
        width: 90%;
        font-size: 40px;
        background-color: steelblue;
        border-radius: 10px;
        border: none;
        margin-bottom: 20px;
        &:hover{
            background-color: rgb(101, 168, 0);
        }
        &:focus{
            background-color: green;
            border: none;
            box-shadow: none;
        }
        &:active{
            background-color: green;
            border: none;
        }
    }
    .backButton{
        margin: 10px;
        width: 90%;
        height: 100px;
        font-size: 40px;
        background-color: darkblue;
        &:hover{
            background-color: rgb(1, 1, 63);
        }
    }

    .incorrect{
        border-color: red;
    }
    .correct{
        border-color: green;
    }
    .form-control{
        border: none;
        border-radius: none;
    }
    .form-control:focus{
        border: none;
        box-shadow: none;
    }
    .verifiedNumber{
        color: green;
    }
</style>