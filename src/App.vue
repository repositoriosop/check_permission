<script setup>
import {initializeApp} from 'firebase/app'
import dayjs from "dayjs";
import custom from "dayjs/plugin/customParseFormat"
import * as database from 'firebase/database'
import {ref} from "vue";


dayjs.extend(custom)

const firebaseApp = initializeApp({
  apiKey: "AIzaSyC2S9SgV8lNZgeMIVBWMWM3OAseiAd53SU",
  authDomain: "sictren.firebaseapp.com",
  databaseURL: "https://sictren.firebaseio.com",
  projectId: "sictren",
  storageBucket: "sictren.appspot.com",
  messagingSenderId: "226466610877",
  appId: "1:226466610877:web:ab56a948386e79d9"
})

// used for the databas refs
const db = database.getDatabase(firebaseApp)


const values = ref()
const values2 = ref([])
const texto = ref("BUSCAR")
const texto2 = ref("BUSCAR")
const inputValue = ref("")
const inputValueTren = ref("")
const onClickListener = async () => {
  texto2.value = "BUSCANDO..."
  if (inputValueTren.value && inputValueTren.value !== "") {

    const dispositivosRef = database.ref(db, "/trenes/registroDispositivos/tablets");

    database.get(dispositivosRef).then(_snap => {
      const result = _snap.val();
      const keys = Object.keys(result)
      const registers = keys.map(item => {
        return {
          key: item,
          dayObj: dayjs(result[item].fechaRegistro, "DD-MM-YYYY HH:mm:ss").unix(),
          ...result[item]
        }
      })
      values2.value = registers.filter(item => item.numeroTren === inputValueTren.value).sort((a, b) => b.dayObj - a.dayObj)
    });
  }

}

const onClickListenermotriz = async () => {

  if(inputValue.value.trim() === "") {
    return
  }

  const starCountRef = database.ref(db, `/trenes/controlRemoto/${inputValue.value}`);

  database.get(starCountRef).then((snapshot) => {
    values.value = snapshot.val();

    if (values.value === null) {
      values.value = "NO SE ENCONTRO MOTRIZ"
      texto.value = "BUSCAR"
      return
    }
    texto.value = "BUSCAR"

  })
}

</script>

<template>
  <main>
    <div class="container" style="max-width: 400px">

      <div class="row">
        <input type="number" v-model="inputValueTren" class="form-control mb-2" placeholder="ESCRIBE EL NRO DE TREN">
      </div>

      <div class="row">
        <button type="button" class="btn btn-success" @click="onClickListener" :disabled="texto === 'BUSCANDO...'">
          {{ texto }}
        </button>

      </div>

      <div class="row mb-4">
        <p>Ultimos registros del tren:</p>
        <div v-for="item in values2.slice(0,4)">
          <p class="mb-0">Fecha: {{item.fechaRegistro}}</p>
          <p class="mb-0">numeroMotriz: {{item.numeroMotriz}}</p>
          <p class="mb-0">complementaria: {{item.numeroMotrizComplementaria}}</p>
          <hr>

        </div>
      </div>


      <div class="row">
        <input type="text" v-model="inputValue" class="form-control mb-2" placeholder="ESCRIBE LA MOTRIZ">
      </div>

      <div class="row">
        <button type="button" class="btn btn-success" @click="onClickListenermotriz" :disabled="texto === 'BUSCANDO...'">
          {{ texto }}
        </button>

      </div>

      <div class="row">
        Permisos de Motriz encontrados:
        <pre>
          {{ values }}
        </pre>
      </div>
    </div>
  </main>
</template>
