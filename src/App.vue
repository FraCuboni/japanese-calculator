<script>
// import

export default{
  name: 'App',
  components:{

  },
  data(){
    return{
      display :'',

      values: [ 7, 8, 9, 'OFF', 4, 5, 6, 'C', 1, 2, 3, 'X', 0, '='],

      factors: [],

      sessionValue : '',

      result:'',
    }
  },
  methods:{
    buttonClick(value){
      // se il numero inserito è minore di 2 caratteri
      if(this.sessionValue.length < 3){

        // se premi C
        if(value === 'C'){
          // svuoto i campi
          this.display = '';
          this.factors = [];
          this.sessionValue = '';
          console.log(this.factors, this.sessionValue)

        // se premi un numero
        }else if(!isNaN(value)){
          // aggiungo il numero al display
          this.display = this.display + value;
          this.sessionValue = this.sessionValue  + value;

        // se premi X
        }else if(value === 'X'){
          // se non ho già fatto una moltiplicazione
          if(!this.factors.length){
            // aggiungo ai fattori il numero inserito
            this.factors.push(this.sessionValue);
            // lo aggiungo al display
            this.display =`${this.display} ${value} `;        
          }
          console.log(this.factors)

        // se premi =
        }else if(value === '='){
          this.factors.push(this.sessionValue)
          this.sessionValue='';
          calculate();
        }

      }
      
    },
    calculate(){
      if(this.factors.length > 1){
          this.result = this.factors[0]*this.factors[1];
          this.display = this.result;
      }
    }
  }
}
</script>

<template>

  <div class="container">
    <div class="calculator">

      <div class="screen">
        <div class="graphics"></div>
        <div class="numbers">{{display}}</div>
      </div>

      <div class="buttons">

        <div  v-for="value in values" class="button">
          <div @click="buttonClick(value)" class="value">{{value}}</div>
        </div>
        <div class="button">
          <div class="value">Git</div>
        </div>
        <div class="button">
          <div class="value">i</div>
        </div>
      </div>

    </div>
  </div>

</template>


<style scoped lang="scss">
@use './styles/partials/variables.scss' as *;

  .container{
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    width: 100%;
    background-color: lightblue;

    .calculator{
      background-color: grey;
      width: 400px;
      aspect-ratio: 1/1.6;
      padding: 20px;
      border-radius: $border_radius;

      .screen{
        height: 40%;
        width: 100%;
        background-color: lightskyblue;
        border-radius: $border_radius;

        .graphics{
          height: 80%;
          width: 100%;
          background-color: black;
        }
        .numbers{  
          height: 20%;
          width: 100%;
          padding: 0 15px;

          // flex
          display: flex;
          justify-content: end;
          align-items: center;
        }
        
      }

      .buttons{
        margin-top: 20px;
        height: calc(60% - 20px);
        width: 100%;

        // flex
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;

        .button{
          width: calc(100% / 4);
          height: calc(100% / 4);

          // flex
          display: flex;
          justify-content: center;
          align-items: center;

          .value{
            transition: 200ms;
            background-color: white;
            height: 80%;
            aspect-ratio: 1/1;
            border-radius: 50%;

            // flex
            display: flex;
            justify-content: center;
            align-items: center;
            &:hover{
              background-color: rgb(232, 232, 232);
            }
          }
        }
      }




    }

  }
</style>
