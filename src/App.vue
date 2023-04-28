<script setup>
import {initializeApp} from 'firebase/app'
import * as database from 'firebase/database'
import {ref} from "vue";

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
  const texto = ref("BUSCAR")
  const inputValue = ref("")
  const onClickListener = async () => {
  texto.value = "BUSCANDO..."
   if(inputValue.value.trim() !== "") {

     const starCountRef = database.ref(db, `/trenes/controlRemoto/${inputValue.value}`);

     database.onValue(starCountRef, (snapshot) => {

       values.value = snapshot.val();

       if(values.value === null) {
         values.value = "NO SE ENCONTRO MOTRIZ"
       }
       texto.value = "BUSCAR"

     });
   }

  }

</script>

<template>
  <input type="text" v-model="inputValue" class="form-control mb-2" placeholder="ESCRIBE LA MOTRIZ">
  <button type="button"  class="btn btn-success" @click="onClickListener" :disabled="texto === 'BUSCANDO...'">{{texto}}</button>


  <pre>
    {{values}}
  </pre>
</template>
