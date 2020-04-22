<template>  
    <div class="container-fluid cont">

        <!-- Header (TOYA SP. Z O.O.) -->
        <div v-show="numberVerified == false" v-bind:class="mainClass"><h2>{{title}}</h2></div>

        <!-- Login -->
        <div v-show="login == false" class="nrInput"><b-form-input v-model="user" placeholder="Użytkownik"></b-form-input></div>
        <div v-show="login == false" class="nrInput"><b-form-input type="password" v-model="password" placeholder="Hasło"></b-form-input></div>

         <!-- Login Error information -->
        <div v-show="loginError == true" v-bind:class="mainClass" class="error">{{loginErrorMessage}}</div>
        <div v-show="login == false" class="row justify-content-center"><b-button class="nrButton" v-on:click="userCheck">ZALOGUJ</b-button></div>

        <!-- Customer ID Input -->
        <div v-show="numberVerified == false && login == true" v-bind:class="[userNumber.length === 8 ? correct : incorrect, mainClass]" class="nrInput"><b-form-input v-model="userNumber" id="nrAbonenta" type="number" placeholder="Kod Abonenta"></b-form-input></div>
        <div v-show="numberVerified == false && userNumber.length === 8" class="row justify-content-center "><b-button class="nrButton" v-on:click="numberCheck">ZATWIERDŹ</b-button></div>

        <!-- Customer ID Input Information Status -->
        <div v-show="userNumber.length === 0 && login == true" v-bind:class="mainClass">Wpisz 8-cyfrowy Kod Abonenta</div>
        <div v-show="userNumber.length < 8 && userNumber.length > 0" v-bind:class="mainClass">Numer posiada za mało cyfr</div>
        <div v-show="userNumber.length > 8" v-bind:class="mainClass">Numer posiada za dużo cyfr</div>
        <div v-show="numberNotVerified === true && userNumber === wrongUserNumber" v-bind:class="mainClass">Błąd: '{{responseDataInfo}}'</div>
        
        <!-- Verified Customer ID Number -->
        <p v-show="numberVerified == true" v-bind:class="mainClass">{{userAddress}}</p>

        <!-- Devices Categories Buttons -->
        <div v-show="numberVerified == true && categoryChosen == false" v-bind:class="mainClass"><b-button class="mediaButton" v-on:click="showInputs('intFon')">INTERNET / TELEFON</b-button></div>
        <div v-show="numberVerified == true && categoryChosen == false" v-bind:class="mainClass"><b-button class="mediaButton" v-on:click="showInputs('tv')">TELEWIZJA</b-button></div>
        <div v-show="numberVerified == true && categoryChosen == false" v-bind:class="mainClass"><b-button class="mediaButton" v-on:click="showInputs('hdd')">DYSK HDD</b-button></div>

        <!-- Total Number Of Succesfully Added Devices -->
        <div v-show="numberVerified == true && categoryChosen == false" v-bind:class="mainClass">Liczba dodanych urządzeń: {{addedDeviceNumber}}</div>

        <!-- Devices Numbers Inputs -->
        <div v-show="devices.tv || devices.intFon || devices.hdd == true" v-bind:class="[devices.hdd ? (idDevice1.length === 14 ? correct : incorrect) : (idDevice1.length === 11 || idDevice1.length === 16 ? correct : incorrect), mainClass]" class="nrInput">
            <b-form-input id="ident1" v-model="idDevice1" v-bind:placeholder="devices.tv == true ? placeholderKarta : (devices.hdd == true ? placeholderDysk : placeholderInternet)" autocomplete="false" >
            </b-form-input>
            <button class="cameraButton" v-on:click="() => this.showCamera(1)">
                <b-icon-camera class="icon" scale="2"></b-icon-camera>
            </button>
        </div>
        <div v-show="devices.tv == true" v-bind:class="[idDevice2.length === 11 ? correct : incorrect, mainClass]" class="nrInput">
            <b-form-input id="ident2" v-model="idDevice2" v-bind:placeholder="placeholderDekoder" autocomplete="false">
            </b-form-input>
            <button class="cameraButton" v-on:click="() => this.showCamera(2)">
                <b-icon-camera class="icon" scale="2"></b-icon-camera>
            </button>
        </div>
        <div v-show="camera === true" id="scannerContainer" class="scannerContainer">
            <div v-show="cameraLoaded === true" class="overlay"><b-icon-arrow-bar-up class="arrow" scale="8"></b-icon-arrow-bar-up></div>
            <div v-show="cameraLoaded === true" class="overlay2"><b-icon-arrow-bar-bottom class="arrow" scale="8"></b-icon-arrow-bar-bottom></div>
        </div>
        <div v-show="(devices.tv || devices.intFon || devices.hdd == true) && deviceAdded == false" v-bind:class="mainClass"><b-button class="nrButton" v-on:click="addDevice">DODAJ</b-button></div>

        <!-- Devices Posting Information Status -->
        <div v-show="deviceAdded == true" v-bind:class="mainClass" class="verifiedNumber"><span >Sukces: '{{responseDataInfo}}'</span></div>
        <div v-show="deviceAddError == true" v-bind:class="mainClass" class="error"><span >Błąd: '{{responseDataInfo}}'</span></div>
        
        <!-- Go Back Button -->
        <div v-show="devices.tv || devices.intFon || devices.hdd == true" v-bind:class="mainClass"><b-button class="backButton" v-on:click="goBack">WRÓĆ</b-button></div>
        <div v-show="numberVerified && !categoryChosen" v-bind:class="mainClass"><b-button class="backButton" v-on:click="goBackAbonent">WRÓĆ</b-button></div>
    </div>
</template>

<script>
import axios from 'axios';
import Quagga from 'quagga';

export default {
    name: 'TerainApp',
    props: {
        title: String
    },
    created: function(){
        let token = localStorage.getItem('token');
        if(token){
            axios
                .get(`${this.mainAPI}/v1/popc/checkToken?token=${token}`)
                .then(
                    this.login = true
                )
                .catch(e => (
                    console.log(e.response)
                ))
            this.token = token;
        }
    },
    methods: {
        showCamera: async function(arg){
            if(this.deviceInput){
                Quagga.stop();
            }
            this.camera = true;
            this.deviceInput = arg;
            if(this.devices.tv === true && arg === 1){
                this.codeType = "i2of5_reader";
            }   else if(this.devices.intFon === true){
                this.codeType = "code_128_reader"
            }   else this.codeType = "code_93_reader";
            await this.showLiveCamera();
        },
        numberCheck: async function(){
            event.preventDefault();
            await this.getFromApi();
            this.operatingSystem = this.getMobileOperatingSystem();
        },
        userCheck: async function(){
            event.preventDefault();
            await this.prepareLoginPayload();
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
            this.camera = false;
        },
        goBackAbonent: function(){
            this.numberVerified = false;
            this.userNumber = '';
            this.addedDeviceNumber = 0;
        },
        addDevice: async function(){
            await this.preparePayload();
            this.camera = false;
        },
        preparePayload: function(){
            let chosenDevice = ''
            if(this.devices.tv === true){
                chosenDevice = 'tv'
            }
            else if(this.devices.intFon === true){
                chosenDevice = 'netvoip'
            }
            else if(this.devices.hdd === true){
                chosenDevice = 'hdd'
            }
            const payload = {
                type: chosenDevice,
                ident_1: this.idDevice1,
                ident_2: this.idDevice2,
            }
            this.postToApi(payload);
        },
        prepareLoginPayload: function(){
            const payload = {
                login: this.user,
                password: this.password
            }
            this.postLogin(payload);
            this.user = '';
            this.password = '';
        },
        showLiveCamera: async function(){
            let self = this;
            if(self.deviceInput === 1){
                self.idDevice1 = '';
            }   else if(self.deviceInput === 2){
                self.idDevice2 = '';
            }
            await Quagga.init({
                locate: false,
                inputStream: {
                    name: "Live",
                    type: "LiveStream",
                    target: document.getElementById('scannerContainer'),
                    constraints: {
                        width: 1024,
                        height: 720,
                        facingMode: "environment",
                    },
                    area: {
                        top: "45%",
                        right: "0%",
                        left: "0%",
                        bottom: "45%",
                    },
                },
                decoder: {
                    readers: [
                        self.codeType,
                    //   "code_93_reader",
                    //   "ean_8_reader",
                    //   "ean_reader",
                    //   "code_39_vin_reader",
                    //   "code_128_reader",
                    //   "code_39_reader",
                    //   "codabar_reader",
                    //   "upc_reader",
                    //   "upc_e_reader",
                    //   "i2of5_reader",
                    //   "2of5_reader",
                    ],
                },
            }, function (err) {
                if (err) {
                    console.log(err);
                    return
                }
                console.log("Initialization finished. Ready to start");
                Quagga.start();
                self.cameraLoaded = true;
            });

            Quagga.onProcessed(function (result) {
                var drawingCtx = Quagga.canvas.ctx.overlay,
                drawingCanvas = Quagga.canvas.dom.overlay;
                drawingCanvas.style.display = 'none';

                if (result) {
                    if (result.boxes) {
                        drawingCtx.clearRect(0, 0, parseInt(drawingCanvas.getAttribute("width")), parseInt(drawingCanvas.getAttribute("height")));
                        result.boxes.filter(function (box) {
                            return box !== result.box;
                        }).forEach(function (box) {
                            Quagga.ImageDebug.drawPath(box, { x: 0, y: 1 }, drawingCtx, { color: "green", lineWidth: 2 });
                        });
                    }

                    if (result.box) {
                        Quagga.ImageDebug.drawPath(result.box, { x: 0, y: 1 }, drawingCtx, { color: "#00F", lineWidth: 2 });
                    }

                    if (result.codeResult && result.codeResult.code) {
                        Quagga.ImageDebug.drawPath(result.line, { x: 'x', y: 'y' }, drawingCtx, { color: 'red', lineWidth: 3 });
                    }
                }
            });


            Quagga.onDetected(function (result) {
              if(result){
                if(result.codeResult && self.codeType === "i2of5_reader") {
                  this.codeNumber = result.codeResult.code;
                    if(self.deviceInput === 1 && this.codeNumber.length === 12 && (this.codeNumber.toString()).charAt(0) === '0'){
                        this.codeNumber = this.codeNumber.slice(0, -1);
                        self.idDevice1 =  this.codeNumber;
                    }
                }
                else if(result.codeResult && self.codeType === "code_128_reader") {
                  this.codeNumber = result.codeResult.code;
                    if(self.deviceInput === 1 && this.codeNumber.length === 16 && (this.codeNumber.toString()).charAt(0) === '4'){
                        self.idDevice1 =  this.codeNumber;
                    }
                }
                else if(result.codeResult) {
                  this.codeNumber = result.codeResult.code;
                    if(self.deviceInput === 1){
                    self.idDevice1 =  this.codeNumber;
                    }   else if (self.deviceInput === 2){
                        self.idDevice2 = this.codeNumber;
                    }
                }
                Quagga.stop()
                self.cameraLoaded = false;
                self.camera = false;
                if(self.operatingSystem !== 'iOS'){
                    window.navigator.vibrate(200);
                }
              }
            });
        },
        postLogin: function(payload){
            axios
                .post(`${this.mainAPI}/v1/popc/auth`, payload)
                .then(response => (
                    this.token = response.data.data.token,
                    this.loginError = false,
                    this.userLogged()
                ))
                .catch(e => {
                    console.log(e.response)
                    if(e.response.status === 500){
                        this.loginErrorMessage = 'Wystąpił nieoczekiwany błąd. Spróbuj ponownie';
                        this.loginError = true;
                    } else {
                        this.loginErrorMessage = 'Podano niewłaściwy login lub hasło. Spróbuj ponownie';
                        this.loginError = true;
                    }
                })
        },
        postToApi: function(payload){
            axios
                .post(`${this.mainAPI}/v1/popc/device/${this.userNumber}?token=${this.token}`, payload)
                .then(response => (
                    this.deviceAdded = true,
                    this.deviceAddError = false,
                    this.addedDeviceNumber += 1,
                    this.responseDataInfo = response.data.data.info
                    ))
                    
                .catch(e => {
                    this.error = e
                    console.log(this.error, `ERROR ${e.response.status}`)
                    this.deviceAddError = true,
                    this.deviceAdded = false
                    if(e.response.status === 500){
                        this.responseDataInfo = 'Wystąpił nieoczekiwany błąd, spróbuj ponownie'
                    }   else {
                        this.responseDataInfo = e.response.data.data.info
                    }
                })
        },
        getFromApi: function(){
            axios
                .get(`${this.mainAPI}/v1/popc/usercode/${this.userNumber}?token=${this.token}`)
                .then(response => (
                    this.userAddress = response.data.data.address,
                    this.numberVerified = true,
                    this.numberNotVerified = false
                ))
                .catch(e => {
                    this.error = e
                    this.numberNotVerified = true,
                    console.log(this.error, `ERROR ${e.response.status}`)
                    if(e.response.status === 500){
                        this.responseDataInfo = 'Wystąpił nieoczekiwany błąd, spróbuj ponownie'
                    }   else {
                        this.responseDataInfo = 'Nie znaleziono abonenta';
                        this.wrongUserNumber = this.userNumber;
                    }
                })
        },
        userLogged: function(){
            if(this.token){
                this.login = true;
                localStorage.setItem('token', this.token)
            }
        },
        getMobileOperatingSystem: function() {
        var userAgent = navigator.userAgent || navigator.vendor || window.opera;
            if (/windows phone/i.test(userAgent)) {
                return "Windows Phone";
            }
            if (/android/i.test(userAgent)) {
                return "Android";
            }
            if (/iPad|iPhone|iPod/.test(userAgent) && !window.MSStream) {
                return "iOS";
            }
            return "unknown";
        },
    },
    data: function() {
        return {
            codeType: "code_93_reader",
            userNumber: '',
            wrongUserNumber: '',
            userAddress: '',
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
            placeholderInternet: 'GPON S/N / SN',
            placeholderKarta: 'Nr Karty Dostępowej',
            placeholderDysk: 'S/No. Dysku HDD',
            placeholderDekoder: 'CHIP ID STB',
            idDevice1: '',
            idDevice2: '',
            addedDeviceNumber: 0,
            camera: false,
            deviceInput: '',
            cameraLoaded: false,
            operatingSystem: '',
            login: false,
            user: '',
            password: '',
            token: '',
            loginError: false,
            mainAPI: 'https://webapi.toya.net.pl',
            testAPI: 'http://217.113.224.208:9900',
            loginErrorMessage: '',
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
        display: flex;
        flex-wrap: nowrap;
        input {
            text-align: center;
            font-size: 50px;
            border-radius: 0;
            height: 70px;
            flex: 5 0 0;
        }
    }
    .nrButton {
        height: 200px;
        width: 90%;
        font-size: 60px;
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
        height: 200px;
        width: 90%;
        font-size: 50px;
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
        font-size: 60px;
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
    .cameraButton{
        background-color: green;
        border-width: 5px;
        border-color: darkgreen;
        flex: 1 0 0;
        &:focus{
            background-color: yellow;
        }
    }
    .scannerContainer {
        position: relative;
        width: 720px;
        margin: 0 auto;
        .overlay {
            position: absolute;
            width: 100%;
            top: 60%;
            left: 0%;
            //margin-left: 50%;
            margin: 0 auto;
            opacity: 0.8;
            z-index: 100;
            height: 40%;
            background-color: black;
            color: white;
            border-top: solid;
            border-top-width: 10px;
            border-color: darkorange;

            .arrow {
                position: absolute;
                bottom: 200px;
                margin-left: -360px;
                width: 100%;
            }
        }
        .overlay2 {
            position: absolute;
            width: 100%;
            top: 0%;
            left: 0%;
            //margin-left: 50%;
            margin: 0 auto;
            opacity: 0.8;
            z-index: 100;
            height: 40%;
            background-color: black;
            color: white;
            border-bottom: solid;
            border-bottom-width: 10px;
            border-color: darkorange;

            .arrow {
                position: absolute;
                top: 200px;
                margin-left: -360px;
                width: 100%;
            }
        }
    }
</style>