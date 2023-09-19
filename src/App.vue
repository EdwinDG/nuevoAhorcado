<template>
  <div class="container">
    <!-- primera pagina -->
    <div class="header-container" v-if="juegoAhorcado === 1">
      <header>
        <h2 class="h2jg" >Juego de Ahorcado</h2>
        <div class="image-container">
          <img
            src="https://st4.depositphotos.com/10614052/25198/i/450/depositphotos_251983410-stock-photo-silhouette-of-male-suicider-going.jpg"
            alt="Imagen del juego">
        </div>
        <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Quod laudantium doloribus enim, deleniti facere ad
          sunt, recusandae praesentium earum cumque iure natus deserunt ducimus qui nobis itaque sed, dicta laboriosam!
        </p>
      </header>
      <button class="btn1" @click="juegoAhorcado = 2">Jugar</button>
    </div>
    <!-- primera pagina -->

    <!-- segunda pagina -->
    <div class="container_dificultad" v-else-if="juegoAhorcado === 2">
      <h3 class="h3juego" >Seleccione la Dificultad</h3>
      <div class="container_boton">
        <button class="btn0" @click="seleccionarDificultad('Fácil')">Facil</button>
        <button class="btn0" @click="seleccionarDificultad('NORMAL')">Normal</button>
        <button class="btn0" @click="seleccionarDificultad('Difícil')">Difícil</button>
      </div>
    </div>
    <!-- segunda pagina -->

    <!-- tercera pagina -->
    <div class="container1" v-else-if="juegoAhorcado === 3">
      <h1>Seleccione la categoría que desea</h1>
      <div class="categorias">
        <div class="card">
          <div class="card-image"><img class="card-image"
              src="https://img.freepik.com/vector-premium/conjunto-animales-lindos-kawaii-ninos-que-aprenden-vocabulario-estilo-dibujos-animados-plana_34141-498.jpg?w=2000"
              alt=""> </div>
          <div class="category"> ANIMALES </div>
          <button class="btn3" @click="seleccionarCategoria('ANIMALES')"
            :class="{ 'categoria-seleccionada': categoriaSeleccionada === 'ANIMALES' }">PLAY</button>
        </div>
        <div class="card">
          <div class="card-image"><img class="card-image"
              src="https://i0.wp.com/unprogramador.com/wp-content/uploads/2017/05/descubre-los-mejores-paises-para-vivir.jpg"
              alt=""></div>
          <div class="category"> PAISES</div>
          <button class="btn3" @click="seleccionarCategoria('PAISES')"
            :class="{ 'categoria-seleccionada': categoriaSeleccionada === 'PAISES' }">PLAY</button>
        </div>
        <div class="card">
          <div class="card-image"><img class="card-image"
              src="https://s3.abcstatics.com/media/sociedad/2022/05/17/nombres-espana-apellidos--620x349.png" alt="">
          </div>
          <div class="category">NOMBRES</div>
          <button class="btn3" @click="seleccionarCategoria('NOMBRES')"
            :class="{ 'categoria-seleccionada': categoriaSeleccionada === 'NOMBRES' }">PLAY</button>
        </div>
        <div class="card">
          <div class="card-image"><img class="card-image"
              src="https://dpplus.es/wp-content/uploads/2017/04/simbologia-del-color.jpg" alt=""></div>
          <div class="category">COLORES</div>
          <button class="btn3" @click="seleccionarCategoria('COLORES')"
            :class="{ 'categoria-seleccionada': categoriaSeleccionada === 'COLORES' }">PLAY</button>
        </div>
        <div class="card">
          <div class="card-image"><img class="card-image"
              src="https://www.barcelona.cat/fabraicoats/centredart/sites/default/files/styles/slideshow/public/icons%201.jpg?itok=4Pkw6h3u"
              alt=""></div>
          <div class="category">COSAS</div>
          <button class="btn3" @click="seleccionarCategoria('COSAS')"
            :class="{ 'categoria-seleccionada': categoriaSeleccionada === 'COSAS' }">PLAY</button>
        </div>
        <button class="btn4" @click="juegoAhorcado = 4">comenzar Juego</button>
      </div>
    </div>
    <!-- tercera pagina -->

    <!-- cuarta pagina -->
    <div class="container2" v-else-if="juegoAhorcado === 4">
      <h1 class="tt" >AHORCADO</h1>
      <div class="container_im_in">
        <img class="img2" :src="juegoPerdido && intentosRestantes() === 0 ? final.perdido[0] : imagenError" alt="">
        <div class="info">
        <h3 class="tt" >INFORMACION</h3>
        <h3>Nivel: {{ dificultadSelec }}</h3>
        <h3>Categoría: {{ categoriaSelec }}</h3>
        <h3>Intentos restantes: {{ intentosRestantes() }}</h3>
      </div>
      </div>
      <div v-if="juegoGanado">
        <p class="mensaje-ganado">¡Ganaste el juego!</p>
      </div>
      <p v-if="juegoPerdido" class="mensaje-perdido">Has perdido el juego</p>
      <h2 class="palabra_oculta">{{ mostrarPalabraOculta() }}</h2>
      <div class="container2_1">
        <button class="btn_letras" v-for="letter in alfabeto" :key="letter" @click="clickLetra(letter)"
          :disabled="usoLetras.includes(letter) || !puedeSeleccionarLetra(letter) || juegoGanado"
          :class="{ 'cambio-color': cambioColor && dificultadSelec === 'Fácil' || dificultadSelec === 'NORMAL' }">
          {{ letter }}
        </button>
        <button class="btn5" @click="reiniciarJuego">🔄</button>
        <button class="btn9" @click="regresarSeleccionDificultad">Volver</button>
        <!-- cuarta pagina -->
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const juegoAhorcado = ref(1);
const usoLetras = ref([]);
const alfabeto = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');
const dificultadSelec = ref('');
const categoriaSelec = ref('');
const dificultadError = ref(0);
const imagenError = ref('');
const categoriaSeleccionada = ref('');
const cambioColor = ref(false);

let errorCount = ref(0);
let palabraSecreta = ref('');
let juegoPerdido = ref(false);
let juegoGanado = ref(false);
let letrasRestantes = ref(0);

const nombreCategoria = {
  ANIMALES: ["PERRO", "GATO", "IGUANA", "LOBO", "SAPOPERRO", "PATO", "COCODRILO", "ELEFANTE", "TIGRE", "ANACONDA", "RANA", "DINOSAURIO", "SERPIENTE", "RINOCERONTE", "ARMADILLO"],
  PAISES: ["BRASIL", "MEXICO", "COLOMBIA", "JAPON", "AUSTRALIA", "RUSIA", "ARGENTINA", "PERU", "SENEGAL", "CHILE", "ESTADOSUNIDOS", "ALEMANIA", "FRANCIA", "CANADA", "ESPAÑA"],
  NOMBRES: ["AMALIA", "LUIS", "MARIA", "CARLOS", "EDWIN", "JOSE", "KEVIN", "ARTURO", "JAIDER", "DAMIAN", "ALBEIRO", "CAMILO", "SERGIO", "FELIPE", "ANDRES", "JULIANA", "KATHERIN"],
  COLORES: ["ROJO", "AZUL", "VERDE", "AMARILLO", "MORADO", "VIOLETA", "NARANJA", "DORADO", "FUCCIA", "MARRON", "CAFE", "PIEL", "NEGRO", "BLANCO", "GRIS", "VINOTINTO", ""],
  COSAS: ["AUTO", "LIBRO", "BICICLETA", "COMPUTADORA", "GLOBO", "CELULAR", "MOTOCIERRA", "PISTOLA", "GRANADA", "TELEVISOR", "LLANTA", "SILLA", "BOTELLA", "CERVESA", "CUCHILLO"],
};

const imagenesErrores = ref({
  'Fácil': [
    'https://i.postimg.cc/WpZxWbM2/img10.png',
    'https://i.postimg.cc/L6WMZZTV/img2.png',
    'https://i.postimg.cc/wBXKP4dW/img3.png',
    'https://i.postimg.cc/hjKFq8DK/img4.png',
    'https://i.postimg.cc/9Mb6X40Z/img5.png',
    'https://i.postimg.cc/3NwPkK06/img6.png',
    'https://i.postimg.cc/7hQj7pgL/img7.png',
    'https://i.postimg.cc/qMzP0vT6/img8.png',
    'https://i.postimg.cc/DyHtz3vj/img9.png',
  ],

  'NORMAL': [
    'https://i.postimg.cc/L6WMZZTV/img2.png',
    'https://i.postimg.cc/wBXKP4dW/img3.png',
    'https://i.postimg.cc/hjKFq8DK/img4.png',
    'https://i.postimg.cc/9Mb6X40Z/img5.png',
    'https://i.postimg.cc/7hQj7pgL/img7.png',
    'https://i.postimg.cc/DyHtz3vj/img9.png',
  ],
  'Difícil': [
    'https://i.postimg.cc/wBXKP4dW/img3.png',
    'https://i.postimg.cc/9Mb6X40Z/img5.png',
    'https://i.postimg.cc/7hQj7pgL/img7.png',
    'https://i.postimg.cc/DyHtz3vj/img9.png',
  ],
});

const final = ref({
  'perdido':[
    'https://portal.33bits.net/wp-content/uploads/2018/12/gameoverphrase.jpg'
  ]
})

function clickLetra(letter) {
  if (dificultadSelec.value === 'Difícil' || !usoLetras.value.includes(letter)) {
    usoLetras.value.push(letter);
    if (palabraSecreta.value.includes(letter)) {
      if (letrasAdivinadas()) {
        juegoGanado = true;
      }
    } else {
      letrasRestantes.value--;
      errorCount.value++;
      if (errorCount.value < dificultadError.value) {
        mostrarImagenError();
      } else {
        juegoPerdido.value = true;
      }
    }
  }
}


function letrasAdivinadas() {
  for (const letra of palabraSecreta.value) {
    if (!usoLetras.value.includes(letra)) {
      return false;
    }
  }
  return true;
}

function seleccionarDificultad(dificultad) {
  dificultadSelec.value = dificultad;
  if (dificultad === 'Fácil' || dificultad === 'NORMAL') {
    cambioColor.value = true;
  } else {
    cambioColor.value = false;
  }
  if (dificultad === 'Fácil') {
    letrasRestantes.value = 10;
    dificultadError.value = 10;
  } else if (dificultad === 'NORMAL') {
    letrasRestantes.value = 7;
    dificultadError.value = 7;
  } else if (dificultad === 'Difícil') {
    letrasRestantes.value = 5;
    dificultadError.value = 5;
  }

  juegoAhorcado.value++;
}


function puedeSeleccionarLetra(letter) {
  return letrasRestantes.value > 0 && !usoLetras.value.includes(letter);
}

function seleccionarCategoria(categoria) {
  categoriaSelec.value = categoria;
  categoriaSeleccionada.value = categoria;
  palabraSecreta = ref(generarPalabraAleatoria(nombreCategoria[categoria]));
  palabraAdivinada.value = generarCadenaConGuiones(palabraSecreta.value);
}

function generarCadenaConGuiones(palabra) {
  return palabra.split('').map(() => '_').join(' ');
}

function generarPalabraAleatoria(palabras) {
  const randomIndex = Math.floor(Math.random() * palabras.length);
  return palabras[randomIndex].toUpperCase();
}

function reiniciarJuego() {
  usoLetras.value = [];
  juegoPerdido.value = false;
  juegoGanado = false;
  letrasRestantes.value = (dificultadSelec.value === 'Fácil') ? 10 : (dificultadSelec.value === 'NORMAL') ? 7 : 5;
  palabraSecreta.value = generarPalabraAleatoria(nombreCategoria[categoriaSelec.value]);
  reiniciarImagenes();
}

function reiniciarImagenes() {
  errorCount.value = 0;
  imagenError.value = '';
}

function intentosRestantes() {
  return dificultadError.value - errorCount.value;
}

function mostrarPalabraOculta() {
  if (juegoPerdido.value) {
    return palabraSecreta.value;
  }

  let palabraMostrada = '';
  for (const letra of palabraSecreta.value) {
    if (usoLetras.value.includes(letra)) {
      palabraMostrada += letra;
    } else {
      palabraMostrada += '_';
    }
    palabraMostrada += ' ';
  }
  return palabraMostrada.trim();
}

function regresarSeleccionDificultad() {
  juegoAhorcado.value = 1;
  categoriaSeleccionada.value = '';
  reiniciarJuego();
}


function mostrarImagenError() {
  const errorIndex = errorCount.value - 1;
  if (errorIndex >= 0 && errorIndex < imagenesErrores.value[dificultadSelec.value].length) {
    imagenError.value = imagenesErrores.value[dificultadSelec.value][errorIndex];
  }
}

</script>

<style scoped>

.h2jg{
  font-size: 40px;
  color: #e9d9d9;
}

.h3juego{
  font-size: 50px;
}

.container {
  width: 100%;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: 'Indie Flower', cursive;
}

.container1 {
  text-align: center;
}

.categorias {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.card {
  flex: 0 1 calc(10% - 15px);
  background: white;
  padding: .5em;
  border-radius: 6px;
  box-shadow: 0 4px 6px rgb(0, 0, 0);
  text-align: center;
}

.card-image {
  background-color: rgb(194, 23, 23);
  width: 100%;
  height: 300px;
  border-radius: 6px 6px 0 0;
}

.card-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.category {
  font-size: 1.7em;
  font-weight: 600;
  color: rgb(0, 0, 0);
  padding: 10px 7px 0;
}


.header-container,
.container_dificultad {
  max-width: 800px;
  padding: 20px;
  text-align: center;
}

.mensaje-ganado,
.mensaje-perdido {
  font-size: 25px;
  text-align: center;
}

.image-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}

.container_boton {
  display: grid;
  gap: 10px;
  width: 100%;
  margin-top: 20px;
}

.btn1,
.btn2 {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  margin-top: 20px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn4 {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  margin-top: 20px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn5 {
  background-color: #007bff;
  color: #fff;
  border: 1px solid black;
  cursor: pointer;
}

.btn9{
  width: 100%;
  font-weight: 700;
  border: 1px solid black;
}

.btn3 {
  padding: 10px 20px;
  background-color: #4e4d4d;
  width: 200px;
  border-radius: 10px;
  color: #fff;
  margin: 20px;
  cursor: pointer;
}

.container2 {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  margin-top: 20px;
}

.info {
  padding: 10px;
  background-color: #e9d9d9;
  border-radius: 15px;
  border: 7px solid black;
  margin: 10px;
  height: 100%;
}
.container_im_in{
  display: flex;
  margin-left: 250px;
}

.img2[src$="gameoverphrase.jpg"] {
  border-color: #ff0000;
  background-color: #ffeeee;
  width: 600px;
}

.palabra_oculta {
  text-align: center;
}

.img2 {
  min-width: 300px;
  height: 450px;
  background: url('https://gifslocosanimados.files.wordpress.com/2012/07/tumblr_m2lom8oeac1qe31nvo1_400.gif') center center no-repeat;
  background-size: cover;
  object-fit: cover;
  border: 5px solid black;
  border-radius: 20px;
  max-width: 100%;
  width: 600px;
}

button.btn3.categoria-seleccionada {
  background-color: #FF5733;
  color: #FFFFFF;
}

.cambio-color {
  background-color: rgb(0, 0, 0);
  color: white;
}

.cambio-color:disabled {
  background-color: rgb(255, 255, 255);
  color: rgb(0, 0, 0);
  cursor: not-allowed;
}

.btn_letras {
  height: 50px;
  width: 50px;
  color: #FFF;
  font-size: 16px;
  font-weight: 600;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  letter-spacing: 2px;
  transition: all 0.5s;
  border: none;
}

.container2_1 {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(50px, 1fr));
  gap: 10px;
  margin-bottom: 20px;
  max-width: 600px;
  width: 100%;
  justify-content: center;
}

@media screen and (max-width: 768px) {
  .container2_1 {
    grid-template-columns: repeat(auto-fill, minmax(40px, 1fr));
  }

  .info {
    font-size: 14px;
  }
}

@media screen and (max-width: 480px) {
  .img2[src$="gameoverphrase.jpg"] {
    width: 100%;
  }
}

</style>
