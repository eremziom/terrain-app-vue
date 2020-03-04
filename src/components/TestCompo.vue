<template>  
    <div class="container-fluid cont">
        <div class="row justify-content-center" ><h2>{{testProp}}</h2></div>
        <div v-bind:class="[userNumber.length === 10 ? correct : incorrect]" class="row justify-content-center nrInput"><b-form-input v-model="userNumber" id="nrAbonenta" type="number" placeholder="Numer Abonenta"></b-form-input></div>
        <div v-show="userNumber.length === 10" class="row justify-content-center "><b-button class="nrButton">WERYFIKUJ</b-button></div>
    </div>
</template>

<script>
export default {
    name: 'TestCompo',
    props: {
        testProp: String
    },
    methods: {
        
    },
    data: function() {
        return {
            userNumber: '',
            correct: 'correct',
            incorrect: 'incorrect',
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
</style>