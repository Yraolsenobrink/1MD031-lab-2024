<template>  
  <div>
    <header id="mainheader">
      <img
        id="headerbild"
        src="https://www.portalrestaurant.se/wp-content/uploads/2023/07/top-restaurangen.jpg"
        alt="headerbild"
        style="width:100%; height:50%;"
      />
      <h1 id="headertext">Välkommen till Yras hamburgarkök!</h1>
    </header>

    <main>
      <section id="burgers-section">
        <h3>Välj hamburgare:</h3>
        <p>Utforska vårt breda utbud av goda hamburgare!</p>
        <div class="burger-grid">
          <Burger v-for="burger in burgers"
            v-bind:burger="burger" 
            v-bind:key="burger.name"
            v-on:orderedBurger="addToOrder($event)"/>
        </div>    
      </section>

      <section id="info-section">
        <h3>Information:</h3>
        <p>Fyll i informationen vi behöver för att kunna leverera din hamburgare</p>
        <section>
          <p>
            <label for="name">För- och efternamn</label><br />
            <input
              type="text"
              id="name"
              v-model= formData.name
              required="required"
              placeholder="För- och efternamn"
            />
          </p>
          <p>
            <label for="email">E-mail adress</label><br />
            <input
              type="email"
              id="email"
              v-model=formData.email
              required="required"
              placeholder="E-mail adress"
            />
          </p>
        </section>

        <section>
          <p>
            <label for="payment">Betalningsmetod</label><br />
            <select id="payment" v-model=formData.payment>
              <option selected="selected">Kreditkort</option>
              <option>Faktura</option>
              <option>Mynt</option>
              <option>Swish</option>
            </select>
          </p>
        </section>

        <section>
          <label>Välj kön</label><br />
          <input type="radio" id="man" v-model=formData.gender value="Man" />
          <label for="man">Man</label><br />

          <input type="radio" id="woman" v-model=formData.gender value="Kvinna" />
          <label for="woman">Kvinna</label><br />

          <input type="radio" id="other" v-model=formData.gender value="Annat" />
          <label for="other">Annat</label><br />

          <input type="radio" id="secret" v-model=formData.gender value="secret" checked/>
          <label for="secret">Hemligt</label>
        </section>

        <section id="map-section">
          <br />
        <label>Klicka i din destination på kartan:</label>
        <div id="map-wrapper">
          <div id="map" v-on:click="setLocation"></div>
        <div class="target" v-bind:style="{left: location.x + 'px', top: location.y + 'px', position: 'absolute'}">
          T
        </div>
      </div>
      </section>

      <section>
          <button type="submit" v-on:click="submitOrder">
            <img
              src="https://e7.pngegg.com/pngimages/554/935/png-clipart-triumph-motorcycles-ltd-triumph-daytona-650-triumph-daytona-675-triumph-daytona-600-motorbike-exhaust-system-car.png"
              style="width:40px; height:auto;"
            />
            Skicka beställning!
          </button>
        </section> 
      
    
      </section>

     
    </main>
    <hr />
    <footer>
      <p>© 2024 Yras Hamburgarkök. Alla rättigheter förbehållna.</p>
    </footer>
  </div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json';

const socket = io("localhost:3000");

function MenuItem(n, pic, kcal, ag) {
    this.name = n; // The *this* keyword refers to the object itself
    this.picture = pic;
    this.kCal = kcal;
    this.allergies = ag;
}

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: menu,
      formData:{
        name: "",
        email: "",
        payment: "Kreditkort",
        gender: "Vill inte säga",
      },
      orderedBurgers: {}, /*Här lagras de beställda hamburgarna*/
      location: { x: 0,
            y: 0
          }
    };
  },

methods: {
  submitOrder: function () {
    console.log("formulärdata",this.formData, JSON.stringify(this.formData, null, 2))
    console.log(this.orderedBurgers); // För att se resultatet i konsolen
    socket.emit("addOrder", {
      orderId: this.getOrderNumber(),
      details: {
        x: this.location.x,
        y: this.location.y
      },
      orderItems: this.orderedBurgers,
      formData: this.formData,
    });
  },
  addToOrder: function (event) {
  this.orderedBurgers[event.name] = event.amount;
  
  },
  getOrderNumber: function () {
    return Math.floor(Math.random() * 100000);
  },
  
  setLocation: function (event) {
    var offset = {
      x: event.currentTarget.getBoundingClientRect().left,
      y: event.currentTarget.getBoundingClientRect().top
    };
    this.location.x = event.clientX - offset.x;
    this.location.y = event.clientY - offset.y;
  }
}
}


</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Slackey&display=swap');
  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
    background-size: cover; /* Gör så att bilden fyller hela div-yta proportionellt */
    overflow: hidden; /* Döljer delar som är utanför gränserna */
  }

  #map-wrapper {
    width: 800px; /* Justera bredden */
    height: 500px; /* Justera höjden */
    overflow: scroll; /* Gör det möjligt att skrolla */
    border: 1px solid black; /* Lägg till en ram om det behövs */
    position: relative;
}

  @media screen and (max-width: 800px) {
    h1 {
        font-size: 6vw;
    }
}

body {
    font-size: 13pt;
    font-family: "Times New";
}

.p {
    color: white;
}

.highlight {
    font-weight: bold;
 }
 
 #burgers-section {
    background-color: white; 
    color: rgb(91, 3, 3);
    margin: 30px auto;
    padding: 20px;
    border: 2px dashed rgb(91, 3, 3);
    text-align: center; /* Centrerar rubrik och text */
}

.burger-grid {
    display: grid; /* Grid-layout aktiveras här */
    grid-template-columns: repeat(3, 1fr); /* Tre kolumner */
    gap: 20px; /* Mellanrum mellan hamburgarna */
    margin-top: 20px; /* Skapar avstånd från texten ovanför */
    display: flex; 
    justify-content: center; 
    flex-wrap: wrap;
}

.burger-item{
    padding: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
    max-width: 260px;
}

.burger-item img {
    width: auto; /* Responsiv */
    max-width: 200px; /* Begränsad bredd */
    height: 200px;
}

#info-section {
    background-color: rgb(91, 3, 3); 
    color: white;
    margin: 30px auto; /* Skapar extra utrymme ovanför och under sektionen */
    padding: 20px; /* Utrymme runt hela innehållet */
    border: 2px dashed white; /* Tjock, streckad svart ram */
    padding-left: 30px; /* Extra utrymme till vänster innanför ramen */
}

#mainheader {
    position: relative;
}    

#headertext {
    position: absolute;
    top: 50%; /* Centrera texten vertikalt */
    left: 50%; /* Centrera texten horisontellt */
    transform: translate(-50%, -50%); /* Justera för att centrera korrekt */
    color: black; /* Gör texten helt svart */
    overflow: hidden;
    margin-top: -20px; /* Testa olika negativa värden för att justera höjden om det behövs */
    z-index: 1; /* Se till att texten ligger ovanpå bilden */
    font-family: 'Slackey', serif; 
    color: rgb(91, 3, 3);
    font-size: 50pt;
}

#headerbild {
    opacity: 0.4;
}
 
button:hover {
    background-color: maroon; 
    color: white;
    cursor: pointer;
 }

 button {
    margin-top: 20px;
    margin-bottom: 15px;
    padding: 10px 20px;
}

main, header, footer, nav ul {
    max-width: 60rem;
    margin: 0 auto 0 auto;
}
main {
    background-color: white;
}

header h1 {
    width:60rem;
    margin: 0 auto;
    text-align: center;
}

.target{
  position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
}
</style>