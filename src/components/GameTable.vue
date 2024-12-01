<template>
    <div>
      <canvas ref="boardCanvas" width="800" height="600"></canvas>
    </div>
  </template>
  
  <script setup>
  import { onMounted, ref } from 'vue';
  
  const boardCanvas = ref(null);
  const pawnPositions = ref([
    { row: 3, col: 0, color: 'red' },
    { row: 3, col: 0, color: 'blue' }
  ]);
  
  const centerImage = new Image();
  centerImage.src = new URL('../assets/a-historia-do-brasil.jpg', import.meta.url).href;
  
  onMounted(() => {
    const canvas = boardCanvas.value;
    const context = canvas.getContext('2d');
  
    const rows = 4;
    const cols = 6;
    const cellWidth = canvas.width / cols;
    const cellHeight = canvas.height / rows;
  
    drawBoard(context, rows, cols, cellWidth, cellHeight);
  
    centerImage.onload = () => {
      const centerX = cellWidth * 1;
      const centerY = cellHeight * 1;
      const centerWidth = cellWidth * 4;
      const centerHeight = cellHeight * 2;
      
      context.drawImage(centerImage, centerX, centerY, centerWidth, centerHeight);
    };
  
    drawAllPawns(context, cellWidth, cellHeight);
  });
  
  function drawBoard(context, rows, cols, cellWidth, cellHeight) {
    for (let row = 0; row < rows; row++) {
      for (let col = 0; col < cols; col++) {
        if (row === 3 && col === 0) {
          context.fillStyle = '#ffcc00';
        } else if ((row + col) % 2 === 0) {
          context.fillStyle = '#ffffff';
        } else {
          context.fillStyle = '#000000';
        }
        if ((row === 1 || row === 2) && (col === 1 || col === 2 || col === 3 || col === 4)) {
          continue;
        }
        context.fillRect(col * cellWidth, row * cellHeight, cellWidth, cellHeight);
      }
    }
  }
  
  function drawAllPawns(context, cellWidth, cellHeight) {
    const cellOccupancy = {};
    pawnPositions.value.forEach((pawn) => {
      const key = `${pawn.row}-${pawn.col}`;
      if (!cellOccupancy[key]) {
        cellOccupancy[key] = [];
      }
      cellOccupancy[key].push(pawn);
    });
  
    Object.values(cellOccupancy).forEach((pawns) => {
      pawns.forEach((pawn, index) => {
        const isSinglePawn = pawns.length === 1;
        drawPawn(context, pawn.row, pawn.col, cellWidth, cellHeight, isSinglePawn ? index : index, pawn.color);
      });
    });
  }
  
  function drawPawn(context, row, col, cellWidth, cellHeight, offsetIndex, color) {
    let centerX = col * cellWidth + cellWidth / 2;
    let centerY = row * cellHeight + cellHeight / 2;
  
    if (offsetIndex !== null) {
      const offset = cellWidth / 5;
      centerX += (offsetIndex - 1) * offset;
    }
  
    context.fillStyle = color;
  
    context.beginPath();
    context.moveTo(centerX - cellWidth / 6, centerY + cellHeight / 6);
    context.lineTo(centerX + cellWidth / 6, centerY + cellHeight / 6);
    context.arc(centerX, centerY, cellWidth / 6, 0, Math.PI, true);
    context.closePath();
    context.fill();
  
    context.beginPath();
    context.ellipse(centerX, centerY - cellHeight / 8, cellWidth / 10, cellHeight / 15, 0, 0, 2 * Math.PI);
    context.fill();
  
    context.beginPath();
    context.rect(centerX - cellWidth / 20, centerY - cellHeight / 4, cellWidth / 10, cellHeight / 10);
    context.fill();
  
    context.beginPath();
    context.arc(centerX, centerY - cellHeight / 3, cellWidth / 12, 0, 2 * Math.PI);
    context.fill();
  }
  
  function movePawn(pawnIndex) {
    const canvas = boardCanvas.value;
    const context = canvas.getContext('2d');
    const rows = 4;
    const cols = 6;
    const cellWidth = canvas.width / cols;
    const cellHeight = canvas.height / rows;
  
    context.clearRect(0, 0, canvas.width, canvas.height);
  
    let pawn = pawnPositions.value[pawnIndex];
    if (pawn.row === 3 && pawn.col < cols - 1) {
      pawn.col++;
    } else if (pawn.col === cols - 1 && pawn.row > 0) {
      pawn.row--;
    } else if (pawn.row === 0 && pawn.col > 0) {
      pawn.col--;
    } else if (pawn.col === 0 && pawn.row < rows - 1) {
      pawn.row++;
    }
  
    pawnPositions.value[pawnIndex] = pawn;
  
    drawBoard(context, rows, cols, cellWidth, cellHeight);
    drawAllPawns(context, cellWidth, cellHeight);
  
    const centerX = cellWidth * 1;
    const centerY = cellHeight * 1;
    const centerWidth = cellWidth * 4;
    const centerHeight = cellHeight * 2;
    context.drawImage(centerImage, centerX, centerY, centerWidth, centerHeight);
  }
  
  function getPawnPosition(pawnIndex) {
    return pawnPositions.value[pawnIndex];
  }
  
  function resetPawns() {
    pawnPositions.value = [
      { row: 3, col: 0, color: 'red' },
      { row: 3, col: 0, color: 'blue' }
    ]; 
    
    const canvas = boardCanvas.value;
    const context = canvas.getContext('2d');
    const rows = 4;
    const cols = 6;
    const cellWidth = canvas.width / cols;
    const cellHeight = canvas.height / rows;
  
    context.clearRect(0, 0, canvas.width, canvas.height);
    drawBoard(context, rows, cols, cellWidth, cellHeight);
    drawAllPawns(context, cellWidth, cellHeight);
    
    const centerX = cellWidth * 1;
    const centerY = cellHeight * 1;
    const centerWidth = cellWidth * 4;
    const centerHeight = cellHeight * 2;
    context.drawImage(centerImage, centerX, centerY, centerWidth, centerHeight);
  }
  
  defineExpose({ movePawn, getPawnPosition, resetPawns });
  </script>
  
  <style>
  canvas {
    border: 2px solid #333;
  }
  button {
    margin: 5px;
  }
  </style>