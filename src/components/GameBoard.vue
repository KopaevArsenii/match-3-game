<template>
  <div class="game-board">
    <div
        v-for="(row, rowIndex) in board"
        :key="rowIndex"
        class="row"
    >
      <div
          v-for="(cell, colIndex) in row"
          :key="colIndex"
          class="cell"
          :style="{ backgroundColor: cell.color }"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      boardSize: 8, // Начальный размер поля 8x8
      board: []
    };
  },
  methods: {
    generateBoard() {
      const colors = ["#FF5733", "#33FF57", "#3357FF", "#FF33A1", "#FF9933", "#33FFF3", "#F333FF", "#33FFD6"];
      this.board = Array.from({ length: this.boardSize }, () =>
          Array.from({ length: this.boardSize }, () => ({
            color: colors[Math.floor(Math.random() * colors.length)]
          }))
      );
    }
  },
  created() {
    this.generateBoard();
  }
};
</script>

<style scoped>
.game-board {
  display: grid;
  grid-template-columns: repeat(8, 40px); /* 8 колонок по 40px */
  grid-template-rows: repeat(8, 40px); /* 8 строк по 40px */
  gap: 4px;
  width: 100%; /* Занимает всю ширину родителя */
  height: 100%; /* Занимает всю высоту родителя */
}

.row {
  display: contents; /* Используем содержимое как отдельные ячейки в сетке */
}

.cell {
  width: 40px;
  height: 40px;
  border-radius: 50%; /* Круглые ячейки */
  background-color: gray; /* Цвет фишек */
}
</style>
