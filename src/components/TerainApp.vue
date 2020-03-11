<template>  
    <div class="container-fluid cont">

        <BarcodeReader v-bind:category="categoryChosen" />

        <!-- Header (TOYA SP. Z O.O.) -->
        <div v-show="numberVerified == false" v-bind:class="mainClass"><h2>{{testProp}}</h2></div>

        <!-- Customer ID Input -->
        <div v-show="numberVerified == false" v-bind:class="[userNumber.length === 8 ? correct : incorrect, mainClass]" class="nrInput"><b-form-input v-model="userNumber" id="nrAbonenta" type="number" placeholder="Numer Abonenta"></b-form-input></div>
        <div v-show="numberVerified == false && userNumber.length === 8" class="row justify-content-center "><b-button class="nrButton" v-on:click="numberCheck">ZATWIERDŹ</b-button></div>

        <!-- Customer ID Input Information Status -->
        <div v-show="userNumber.length === 0" v-bind:class="mainClass">Wpisz 8-cyfrowy numer abonenta</div>
        <div v-show="userNumber.length < 8 && userNumber.length > 0" v-bind:class="mainClass">Numer posaida za mało cyfr</div>
        <div v-show="userNumber.length > 8" v-bind:class="mainClass">Numer posiada za duzo cyfr</div>
        <div v-show="numberNotVerified === true" v-bind:class="mainClass">Numer nie został poprawnie zweryfikowany</div>

        <!-- Verified Customer ID Number -->
        <p v-show="numberVerified == true" v-bind:class="mainClass">Nr abonenta: {{userNumber}}</p>

        <!-- Devices Categories Buttons -->
        <div v-show="numberVerified == true && categoryChosen == false" v-bind:class="mainClass"><b-button class="mediaButton" v-on:click="showInputs('intFon')">INTERNET / TELEFON</b-button></div>
        <div v-show="numberVerified == true && categoryChosen == false" v-bind:class="mainClass"><b-button class="mediaButton" v-on:click="showInputs('tv')">TELEWIZJA</b-button></div>
        <div v-show="numberVerified == true && categoryChosen == false" v-bind:class="mainClass"><b-button class="mediaButton" v-on:click="showInputs('hdd')">DYSK HDD</b-button></div>

        <!-- Total Number Of Succesfully Added Devices -->
        <div v-show="numberVerified == true && categoryChosen == false" v-bind:class="mainClass">Liczba dodanych urządzeń: {{addedDeviceNumber}}</div>

        <!-- Devices Numbers Inputs -->
        <div v-show="devices.tv || devices.intFon || devices.hdd == true" v-bind:class="mainClass" class="nrInput"><b-form-input id="ident1" v-model="idDevice1" v-bind:placeholder="devices.tv == true ? placeholderKarta : (devices.hdd == true ? placeholderDysk : placeholderInternet)"></b-form-input></div>
        <div v-show="devices.tv == true" v-bind:class="mainClass" class="nrInput"><b-form-input id="ident2" v-model="idDevice2" v-bind:placeholder="placeholderDekoder"></b-form-input></div>
        <div v-show="(devices.tv || devices.intFon || devices.hdd == true) && deviceAdded == false" v-bind:class="mainClass"><b-button class="nrButton" v-on:click="addDevice">DODAJ</b-button></div>

        <!-- Devices Posting Information Status -->
        <div v-show="deviceAdded == true" v-bind:class="mainClass" class="verifiedNumber"><span >Sukces: '{{responseDataInfo}}'</span></div>
        <div v-show="deviceAddError == true" v-bind:class="mainClass" class="error"><span >Bład: '{{responseDataInfo}}'</span></div>
        
        <!-- Go Back Button -->
        <div v-show="devices.tv || devices.intFon || devices.hdd == true" v-bind:class="mainClass"><b-button class="backButton" v-on:click="goBack">WRÓĆ</b-button></div>
    </div>
</template>

<script>
//import axios from 'axios';
import BarcodeReader from './BarcodeReader.vue'

export default {
    
    name: 'TerainApp',
    props: {
        testProp: String
    },
    components: {
        BarcodeReader,
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
            this.idDevice2 = '';
            this.idDevice1 = '';
            this.deviceAdded = false;
            this.deviceAddError = false;
        },
        addDevice: async function(){
            
            await this.preparePayload();

            if((this.idDevice1 === this.idDevice1 && this.idDevice2 === this.idDevice2) || this.responseDataStatus === 1){
                this.deviceAdded = true;
                this.addedDeviceNumber += 1;
            }   else {
                this.deviceAddError = true;
            }
        },
        preparePayload: function(){
            const payload = {
                request: 'protocols_insertdevicepopc',
                kod_abonenta: this.userNumber,
                ident_1: this.idDevice1,
                ident_2: this.idDevice2,
            }
            console.log(payload);
            //this.postToApi(payload);
        }
        // postToApi: function(payload){
        //     axios
        //         .post('http://beta.csi.toya.net.pl/apps/bober/api/', payload)
        //         .then(response => (
        //             this.responseDataInfo = response.data.info,
        //             this.responseDataStatus = response.data.status))
        //         .catch(e => {
        //             this.error = e
        //         })
        // }
    },
    data: function() {
        return {
            userNumber: '',
            mainClass: 'row justify-content-center',
            correct: 'correct',
            incorrect: 'incorrect',
            numberVerified: false,
            numberNotVerified: false,
            categoryChosen: false,
            deviceAdded: false,
            deviceAddError: false,
            responseDataInfo: null,
            responseDataStatus: null,
            error: null,
            devices: {
                tv: false,
                intFon: false,
                hdd: false
            },
            placeholderInternet: 'ID HFC / GPON',
            placeholderKarta: 'ID Karty STB',
            placeholderDysk: 'ID Dysku HDD',
            placeholderDekoder: 'ID Urządzenia STB',
            idDevice1: '',
            idDevice2: '',
            addedDeviceNumber: 0,
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
        margin: 10px;
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
        border-radius: 10px;
        border: none;
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
        color: white;
        background-color: green;
    }
    .error{
        color: white;
        background-color: red;
    }
</style>