<template>
    <div class="h-screen flex items-center justify-center bg-gradient-to-br from-indigo-500 via-purple-500 to-pink-500">
      <div class="qr-code-generator flex flex-col items-center p-6 bg-white text-gray-800 rounded-xl shadow-xl w-full max-w-md max-h-[80vh]">
        <h2 class="text-3xl font-extrabold mb-4 tracking-wide text-purple-600">✨ Générateur de Code QR ✨</h2>
        <input
          v-model="inputText"
          placeholder="Entrez le texte ou l'URL"
          required
          @input="clearError"
          class="p-3 mb-3 w-full rounded-md border border-gray-300 bg-gray-50 text-gray-800 placeholder-gray-400 shadow-md focus:outline-none focus:ring-2 focus:ring-purple-300 transition-all duration-300 ease-in-out"
        />
        <p v-if="error" class="text-pink-500 font-semibold mb-2">⚠️ Vous devez entrer quelque chose !</p>
        <button 
          @click="generateQRCode" 
          class="bg-gradient-to-r from-pink-600 to-purple-600 hover:from-pink-500 hover:to-purple-500 text-white font-bold py-2 px-6 rounded-md shadow-md transform hover:scale-105 transition-transform duration-300 ease-out"
        >
          Générer le QR Code
        </button>
        <div v-if="qrCodeUrl" class="qr-code-result mt-6 text-center">
          <h3 class="text-xl font-semibold mb-3 text-purple-600">Votre QR Code :</h3>
          <img 
            :src="qrCodeUrl" 
            alt="QR Code" 
            class="w-40 h-40 object-contain border-2 border-purple-200 rounded-lg shadow-lg transform hover:rotate-1 transition-transform duration-300"
          />
          <a 
            :href="qrCodeUrl" 
            download="qrcode.png" 
            class="download-button mt-4 inline-block bg-green-500 hover:bg-green-400 text-white font-bold py-2 px-6 rounded-md shadow-md transform hover:scale-105 transition-transform duration-300 ease-out"
          >
            📥 Télécharger
          </a>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from "vue";
  import axios from "axios";
  import JsConfetti from "js-confetti"; // Importer js-confetti
  
  const inputText = ref("");
  const qrCodeUrl = ref("");
  const error = ref(false);
  
  // Créer une instance de JsConfetti
  const jsConfetti = new JsConfetti();
  
  // Fonction pour envoyer le texte au backend et récupérer l'URL de l'image QR
  async function generateQRCode() {
    if (!inputText.value) {
      error.value = true;
      return;
    }
  
    try {
      const response = await axios.post("http://localhost:3000/", { text: inputText.value });
      qrCodeUrl.value = `http://localhost:3000${response.data}`;
      inputText.value = ""; // Réinitialisez le champ de texte
      jsConfetti.addConfetti(); // Déclenche l'effet de confetti
    } catch (err) {
      console.error("Erreur lors de la génération du code QR :", err);
      error.value = true;
    }
  }
  
  // Fonction pour effacer le message d'erreur
  function clearError() {
    error.value = false;
  }
  </script>
  