<script>
// import

export default {
  name: 'App',
  components: {},
  data() {
    return {
      display: 0,
      values: [7, 8, 9, '<i class="fa-solid fa-power-off"></i>', 4, 5, 6, 'C', 1, 2, 3, 'X', 0, '='],
      factors: [],
      sessionValue: '',
      result: '',

      moveDistance: 0,

      individualNumbers:[],
      equalpressed : false,

      isPopupActive : false,
    }
  },
  methods: {
    moveElement(){
      this.moveDistance -= 15;
    },

    popUpImput(){
      this.isPopupActive = !this.isPopupActive;
    },

    isHtml(value) {
      // Controlla se il valore contiene tag HTML
      return /<\/?[a-z][\s\S]*>/i.test(value);
    },
    buttonClick(value) {
      console.log('hai premuto', value);
      
      // se il numero inserito è minore di 3 caratteri
      if (this.sessionValue.length < 3 ) {

        // se premi C
        if (value === 'C') {
          // svuoto i campi
          this.display = 0;
          this.factors = [];
          this.sessionValue = '';
          this.result = '';
          this.individualNumbers = [];
          this.equalpressed = false;

        // se premi un numero
        } else if (!isNaN(value) && this.sessionValue.length < 2 && !this.equalpressed){
          // Se display = 0 lo rimuovo
          if(this.display === 0){
            this.display='';
          }
          // scrivo il numero sul display
          this.individualNumbers.push(value);
          this.display = this.display.toString() + value; // Concatenazione in stringa
          this.sessionValue += value; // Concatenazione in stringa
          console.log(this.individualNumbers);

        // se premi X
        } else if (value === 'X' && this.sessionValue && !this.equalpressed) {
          // se non ho già fatto una moltiplicazione
          if (!this.factors.length) {
            // aggiungo ai fattori il numero inserito
            this.factors.push(parseFloat(this.sessionValue)); // Converto in numero
            // Aggiungo 'X' al display
            this.display += ` ${value} `; 
            // Resetto sessionValue
            this.sessionValue = ''; 
          }

          // se individualNumbers non ha il secondo numero lo aggiungo manualmente
          if(!this.individualNumbers[1]){
            this.individualNumbers[1] = 0;
          }

          console.log(this.factors);

        // se premi =
        } else if (value === '=' && this.individualNumbers[2]) {
          // se c'è un numero
          if (this.sessionValue) { 
            this.factors.push(parseFloat(this.sessionValue));
            // Resetta sessionValue
            this.sessionValue = ''; 
            // calcola
            this.calculate();
          }
        }
      }
    },
    calculate() {

      // Se c'è solo un fattore restituisci quel valore
      if (this.factors.length === 1) {
        this.result = this.factors[0];

      // Altrimenti, moltiplica i fattori
      } else if (this.factors.length > 1) {
        this.result = this.factors[0] * this.factors[1];

      // Se non ci sono fattori, restituisci 0
      } else {
        this.result = 0; 
      }
      
      // Mostra il risultato
      this.display = this.result; 
      console.log(this.result);

      // Resetta i fattori
      this.factors = [];
      this.equalpressed = true;
    },

    // metodi per calcolo fittizio della moltiplicazione giapponese
    calculateRightNumber(){
      return this.individualNumbers[1] * this.individualNumbers[3]
    },
    calculateMidNumber(){
      let firstnum = this.individualNumbers[0] * this.individualNumbers[3] ;
      let secondnum = this.individualNumbers[1] * this.individualNumbers[2];
      return firstnum + secondnum;
    },
    calculateLeftNumber(){
      return this.individualNumbers[0] * this.individualNumbers[2];
    },
    // metodo per la creazione numero finale
    calculateJP(){
      // assegno delle variabili per dei valori più intuitivi
      let firstnum = this.calculateRightNumber();
      let secondnum = this.calculateMidNumber();
      let thirdnum = this.calculateLeftNumber();
      console.log(firstnum, secondnum, thirdnum);

      // ottengo decina del primo numero se esistente
      if(firstnum >= 10){
        let tens = Math.floor(firstnum / 10);
        // ottengo le decine di firstnum
        console.log(tens);
        firstnum = firstnum - tens * 10;
        secondnum += tens;
        console.log('prima cifra:',firstnum,'seconda cifra pre op:', secondnum);

      }
      if(secondnum >= 10){
        let tens = Math.floor(secondnum / 10);
        // ottengo le decine di firstnum
        console.log('secondnum tens',tens);
        secondnum = secondnum - tens * 10;
        thirdnum += tens;
        console.log('post op second',secondnum , '3rd num completed', thirdnum);
      }

      // compongo il numero finale 
      let result = 
        (isNaN(thirdnum) ? "" : String(thirdnum)) + 
        (isNaN(secondnum) ? "" : String(secondnum)) + 
        (isNaN(firstnum) ? "" : String(firstnum));
          
      return result;
    }
  }
}
</script>

<template>

  <div class="container">
    <img :style="{ transform: 'translateX(' + moveDistance + 'px)' }" class="easteregg" src="../public/catpng.png" alt="">
    <div class="calculator">

      <div class="screen">
        <div class="numbers">{{display}}</div>
      </div>

      <div class="buttons">

        <div  v-for="value in values" class="button">
          <div v-if="!isHtml(value)" @click="buttonClick(value)" class="value">{{value}}</div>
          <div v-else @click="moveElement()" class="value" v-html="isHtml(value) ? value : null"></div>
        </div>
        <div class="button">
          <div class="value"><a href="https://github.com/FraCuboni"><i class="fa-brands fa-github"></i></a></div>
        </div>
        <div class="button">
          <div @click="popUpImput()" class="value"><i class="fa-solid fa-info"></i></div>
        </div>
      </div>
    </div>
    
    <div class="graphics-container">
      <div class="graphics-box">

        <!-- box del primo numero -->
        <div class="first-g-box">

          <div class="first-number">
            <div v-for="n in individualNumbers[0]" class="number-line"></div>
          </div>

          <div class="second-number">
            <div v-for="n in individualNumbers[1]" class="number-line"></div>
          </div>

        </div>

        <div class="second-g-box">

          <div class="first-number">
            <div v-for="n in individualNumbers[2]" class="number-line"></div>
          </div>

          <div class="second-number">
            <div v-for="n in individualNumbers[3]" class="number-line"></div>
          </div>

        </div>

        <div v-if="equalpressed" class="circle left"></div>
        <div v-if="equalpressed" class="circle right"></div>

        <div class="graphic-values">
          <!-- fattori -->
          <div v-if="individualNumbers[0]" class="gv1">{{ individualNumbers[0] }}</div>
          <div v-if="individualNumbers[1]" class="gv2">{{ individualNumbers[1] }}</div>
          <div v-if="individualNumbers[2]" class="gv3">{{ individualNumbers[2] }}</div>
          <div v-if="individualNumbers[3]" class="gv4">{{ individualNumbers[3] }}</div>

          <div class="intersection-box">

            <!-- valori -->
            <div v-if="individualNumbers[0] && individualNumbers[2]" class="left-intersection">{{ calculateLeftNumber() }}</div>
            <div v-if="individualNumbers[0] && individualNumbers[3]" class="top-intersection">{{ this.individualNumbers[0] * this.individualNumbers[3] }}</div>
            <div v-if="individualNumbers[1] && individualNumbers[2]" class="bottom-intersection">{{ this.individualNumbers[1] * this.individualNumbers[2] }}</div>
            <div v-if="individualNumbers[1] && individualNumbers[3]" class="right-intersection">{{ calculateRightNumber() }}</div>

          </div>
        </div>

      </div>
      <div class="explanation-box">
        <h3>what's happening:</h3>
        
      </div>
    </div>


  </div>

  <div v-if="isPopupActive" class="popup-bg">
    <div class="popup">
      <div @click="popUpImput()" class="div">X</div>
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
    gap: 5%;
    position: relative;

    .easteregg {
      position: absolute;
      height: 300px;
      top: 20%;
      left: 27%;
      z-index: 2;
    }

    .moving {
      transform: translateX(-100px); /* Movimento orizzontale */
    }

    // calcolatrice
    .calculator{
      background-color: grey;
      width: 400px;
      aspect-ratio: 1/1.6;
      padding: 20px;
      border-radius: $border_radius;
      z-index: 3;
      font-family: "Titillium Web", sans-serif;

      .screen{
        height: 40%;
        width: 100%;
        background-color: lightskyblue;
        border-radius: $border_radius;

        .graphics{
          height: 80%;
          width: 100%;
          display: flex;
          justify-content: center;
          align-items: center;
          padding: 5px;
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

            &:active{
              box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.5);
              scale: 0.97;
            }
          }
        }
      }
    }

    // lato grafico moltiplicazioni
    .graphics-container{
      background-image: url(../public/AdobeStock_106267755_Preview.jpeg);
      background-position: bottom right;
      padding: 30px;
      width: 450px;
      aspect-ratio: 1/1.4;
      font-family: "Caveat", sans-serif;

      // flex
      display: flex;
      justify-content: space-around;
      align-items: center;
      flex-direction: column;

      .graphics-box{
        margin-top: 35px;
        width: 80%;
        aspect-ratio: 1/1;
        position: relative;
        rotate: 45deg;

  
        .first-g-box, 
        .second-g-box{
          padding:0 10%;
          width: 100%;
          height: 100%;

          display: flex;
          justify-content: space-between;
          align-items: center;

          .first-number,
          .second-number{
            height: 100%;
            width: 30%;
            display: flex;
            justify-content: space-around;
            align-items: center;

            .number-line{
              background-image: url(../public/AdobeStock_106267755_Preview.jpeg);
              background-color: red;
              background-blend-mode: overlay; /* Permette la fusione del colore di sfondo con l'immagine */
              width: 3%;
              height: 100%;
            }
          }
        }

        .second-g-box{
          position: absolute;
          rotate: 90deg;
          transform: scaleX(-1);
          top: 0;
          right: 0;
        }

        .circle{
          position: absolute;
          height: 800px;
          aspect-ratio: 1/1;
          border: 2px solid lightblue;
          border-radius: 50%;
          transform: translate(0, 0);
        }
        .circle.left{
          top: 25%;
          right: 25%;
        }
        .circle.right{
          bottom: 25%;
          left: 25%;
        }

        .graphic-values{
          top: 5%;
          left: 5%;
          width: 90%;
          height: 90%;
          position: absolute;
          .gv1{
            position: absolute;
            rotate: -45deg;
            top: -25%;
            left: 15%;

          }
          .gv2{
            position: absolute;
            rotate: -45deg;
            top: -25%;
            right: 15%;
            
          }
          .gv3{
            position: absolute;
            rotate: -45deg;
            left: -25%;
            bottom: 15%;
            
          }
          .gv4{
            position: absolute;
            rotate: -45deg;
            left: -25%;
            top: 15%;
            
          }

          .intersection-box{
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) rotate(-45deg); /* Centra l'elemento e ruotalo */
            width: 90%;
            height: 90%;
            border: 1px solid black;
            opacity: 0.5;
            z-index: 4;



            .left-intersection,
            .top-intersection,
            .bottom-intersection,
            .right-intersection{
              transform: translate(-50%, -50%);
              position: absolute;
              height: 110px;
              width: 110px;
              display: flex;
              justify-content: center;
              align-items: center;
              border: 1px solid black;
              border-radius: 50%;

            }


            // posizioni iniziali
            .right-intersection{
              top: 50%;
              left: 100%;
            }
            .top-intersection{
              top: 0;
              left: 50%;
            }
            .bottom-intersection{
              top: 100%;
              left: 50%;
            }
            .left-intersection{
              top: 50%;
              left: 0;
            }


            // modified values 4 animation--------------------------------------------------------------------------------------------------

            // scende in basso
            .right-intersection{
              top: 150%;
              left: 100%;
            }

            // si uniscono in mezzo per formare la loro somma
            .top-intersection{
              top: 0;
              left: 50%;
            }
            .bottom-intersection{
              top: 100%;
              left: 50%;
            }

            // resta fermo per ora
            .left-intersection{
              top: 50%;
              left: 0;
            }
          }
        }

      }

      .explanation-box{
        margin-top: 90px;
        z-index: 2;
        width: 100%;
        aspect-ratio: 1/0.4;
      }
    }
  }

  .popup-bg{
    top: 0;
    position: absolute;
    width: 100%;
    height: 100vh;
    background-color: rgba($color: #000000, $alpha: 0.5);
    z-index: 4;

    // flex
    display: flex;
    justify-content: center;
    align-items: center;
    .popup{
      height: 85%;
      aspect-ratio: 1.4/1;
      background-color: white;
      border-radius: $border_radius;
    }
  }
</style>
