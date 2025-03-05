<template>
  <div class="game-board" @click.self="clearSelection">
    <div
        v-for="(row, rowIndex) in board"
        :key="rowIndex"
        class="row"
    >
      <div
          v-for="(cell, colIndex) in row"
          :key="colIndex"
          class="cell"
          :style="{
          backgroundColor: cell.color,
          border: isSelected(rowIndex, colIndex) ? '2px solid #000' : 'none'
        }"
          @click="handleCellClick(rowIndex, colIndex)"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      boardSize: 8, // Начальный размер поля 8x8
      board: [],
      selectedCell: null, // Храним координаты выбранной ячейки
    };
  },
  methods: {
    generateBoard() {
      const colors = ["#FF5733", "#33FF57", "#3357FF", "#FF33A1", "#FF9933", "#33FFF3", "#F333FF", "#33FFD6"];
      this.board = Array.from({ length: this.boardSize }, () =>
          Array.from({ length: this.boardSize }, () => ({
            color: colors[Math.floor(Math.random() * colors.length)],
          }))
      );
    },
    handleCellClick(rowIndex, colIndex) {
      if (this.selectedCell) {
        const [selectedRow, selectedCol] = this.selectedCell;

        // Проверяем, чтобы клик был на соседней ячейке
        if (
            (Math.abs(selectedRow - rowIndex) === 1 && selectedCol === colIndex) ||
            (Math.abs(selectedCol - colIndex) === 1 && selectedRow === rowIndex)
        ) {
          // Меняем местами фишки
          const temp = this.board[rowIndex][colIndex].color;
          this.board[rowIndex][colIndex].color = this.board[selectedRow][selectedCol].color;
          this.board[selectedRow][selectedCol].color = temp;
        }

        // Сбрасываем выбор и подсветку
        this.selectedCell = null;
      } else {
        // Выбираем фишку и подсвечиваем её
        this.selectedCell = [rowIndex, colIndex];
      }
    },
    clearSelection() {
      // Сбрасываем выбор, если кликнули вне поля
      this.selectedCell = null;
    },
    isSelected(rowIndex, colIndex) {
      // Проверяем, является ли ячейка выбранной
      return this.selectedCell && this.selectedCell[0] === rowIndex && this.selectedCell[1] === colIndex;
    }
  },
  created() {
    this.generateBoard();
  },
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
  transition: border 0.3s ease; /* Плавное изменение рамки */
}
</style>
