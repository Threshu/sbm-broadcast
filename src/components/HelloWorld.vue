<template>
  <v-container class="fill-height">

    <div>
      <v-btn color="primary" @click="downloadAndLogPgn">Pobierz PGN</v-btn>

      <button @click="boardAPI?.toggleOrientation()">
        Toggle orientation
      </button>
      <button @click="boardAPI?.resetBoard()">Reset</button>
      <button @click="boardAPI?.undoLastMove()">Undo</button>
      <button @click="boardAPI?.toggleMoves()">Threats</button>
    </div>
    {{ fen }}
    <TheChessboard
      :board-config="boardConfig"
      :fen="fen"
      @board-created="(api) => (boardAPI = api)"
    />
  </v-container>
</template>
<script setup lang="ts">
import { TheChessboard } from 'vue3-chessboard';
import 'vue3-chessboard/style.css';
import type { BoardApi, BoardConfig } from 'vue3-chessboard';
import { ref } from 'vue';
import { Chess } from 'chess.js';


let boardAPI: BoardApi;
const boardConfig: BoardConfig = {
  coordinates: false,
};

const fen = ref(''); // Tutaj przechowywany będzie FEN


// Logika odpowiedzialna za pobieranie i wyświetlanie zawartości pliku .pgn
async function downloadAndLogPgn() {
  try {
    const currentLocation = window.location.href;
    const pgnFilePath = currentLocation.includes('localhost')
      ? `${location.protocol}//${location.hostname}:${location.port}${location.pathname.replace(/\/[^/]*$/, '/games.pgn')}`
      : '/games.pgn';

    // Pobieranie pliku .pgn
    const response = await fetch(pgnFilePath);
    if (!response.ok) {
      throw new Error("Błąd sieci przy próbie pobrania pliku.");
    }

    // Odczytywanie i logowanie zawartości pliku .pgn
    const pgnContent = await response.text();
    console.log(pgnContent);




      function convertPgnToFen(pgn: any) {
      const chess = new Chess();
      const success = chess.loadPgn(pgn);
console.log(success);


      fen.value = chess.fen();
      console.log(fen.value);
    }



      convertPgnToFen(pgnContent);


  } catch (error) {
    console.error("Wystąpił błąd podczas pobierania pliku .pgn:", error);
  }
}
</script>
