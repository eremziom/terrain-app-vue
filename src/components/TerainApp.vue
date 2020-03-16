<template>  
    <div class="container-fluid cont">

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
        <div v-show="devices.tv || devices.intFon || devices.hdd == true" v-bind:class="[devices.hdd ? (idDevice1.length === 14 ? correct : incorrect) : (idDevice1.length === 11 ? correct : incorrect), mainClass]" class="nrInput">
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
        <div v-show="camera == true" id="scannerContainer" class="scannerContainer"></div>
        <div v-show="(devices.tv || devices.intFon || devices.hdd == true) && deviceAdded == false" v-bind:class="mainClass"><b-button class="nrButton" v-on:click="addDevice">DODAJ</b-button></div>

        <!-- Devices Posting Information Status -->
        <div v-show="deviceAdded == true" v-bind:class="mainClass" class="verifiedNumber"><span >Sukces: '{{responseDataInfo}}'</span></div>
        <div v-show="deviceAddError == true" v-bind:class="mainClass" class="error"><span >Bład: '{{responseDataInfo}}'</span></div>
        
        <!-- Go Back Button -->
        <div v-show="devices.tv || devices.intFon || devices.hdd == true" v-bind:class="mainClass"><b-button class="backButton" v-on:click="goBack">WRÓĆ</b-button></div>
        <div v-show="numberVerified && !categoryChosen" v-bind:class="mainClass"><b-button class="backButton" v-on:click="goBackAbonent">WRÓĆ</b-button></div>
    </div>
</template>

<script>
//import axios from 'axios';
import Quagga from 'quagga';

export default {
    name: 'TerainApp',
    props: {
        testProp: String
    },
    methods: {
        showCamera: function(arg){
            this.camera = true;
            this.deviceInput = arg;
            this.showLiveCamera();
        },
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
            this.camera = false;
        },
        goBackAbonent: function(){
            this.numberVerified = false;
        },
        addDevice: async function(){
            await this.preparePayload();
            this.camera = false;
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
        },
        showLiveCamera: async function(){
            await Quagga.init({
                inputStream: {
                    name: "Live",
                    type: "LiveStream",
                    target: document.getElementById('scannerContainer'),
                    constraints: {
                        width: 400,
                        height: 300,
                        facingMode: "environment",
                    },
                    area: {
                        top: "0%",
                        right: "0%",
                        left: "0%",
                        bottom: "50%",
                    },
                },
                decoder: {
                    readers: [
                      "code_93_reader"
                    ],
                },
            }, function (err) {
                if (err) {
                    console.log(err);
                    return
                }
                console.log("Initialization finished. Ready to start");
                Quagga.start();

            });

            let self = this;

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
                if(result.codeResult) {
                  this.codeNumber = result.codeResult.code;
                    if(self.deviceInput === 1){
                    self.idDevice1 =  this.codeNumber;
                    }   else if (self.deviceInput === 2){
                        self.idDevice2 = this.codeNumber;
                    }
                }
              }
            });
        },
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
            camera: false,
            deviceInput: '',
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
            font-size: 20px;
            border-radius: 0;
            height: 70px;
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
        height: 100px;
        width: 90%;
        font-size: 20px;
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
    .cameraButton{
        background-color: green;
        border-width: 5px;
        border-color: darkgreen;
        width: 60px;
        &:focus{
            background-color: yellow;
        }
    }
</style>