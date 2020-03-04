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
        <div v-show="numberVerified == true" class="row justify-content-center "><b-button class="mediaButton">INTERNET / TELEFON</b-button></div>
        <div v-show="numberVerified == true" class="row justify-content-center "><b-button class="mediaButton">TELEWIZJA</b-button></div>
        <div v-show="numberVerified == true" class="row justify-content-center "><b-button class="mediaButton">DYSK HDD</b-button></div>
    </div>
</template>

<script>
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
        }
    },
    data: function() {
        return {
            userNumber: '',
            correct: 'correct',
            incorrect: 'incorrect',
            numberVerified: false,
            numberNotVerified: false,
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
    .btn-secondary:not(:disabled):not(.disabled):active {
        background-color: rgb(10, 78, 10);
        border: none;
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