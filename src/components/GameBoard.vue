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
      const colors = [
        "#FF0000",
        "#024217",
        "#0000FF",
        "#FFFF00",
        "#FF00FF",
        "#00FFFF",
        "#a87532",
        "#28fa6e"
      ];
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

          // Проверяем, образовались ли ряды
          this.checkAndClearLines();
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
    },
    checkAndClearLines() {
      let updatedBoard = [...this.board];

      // Проверка горизонтальных линий
      for (let row = 0; row < this.boardSize; row++) {
        for (let col = 0; col < this.boardSize - 2; col++) {
          const color = this.board[row][col].color;
          if (
              color === this.board[row][col + 1].color &&
              color === this.board[row][col + 2].color
          ) {
            // Очистка ряда
            updatedBoard[row][col].color = "";
            updatedBoard[row][col + 1].color = "";
            updatedBoard[row][col + 2].color = "";
          }
        }
      }

      // Проверка вертикальных линий
      for (let col = 0; col < this.boardSize; col++) {
        for (let row = 0; row < this.boardSize - 2; row++) {
          const color = this.board[row][col].color;
          if (
              color === this.board[row + 1][col].color &&
              color === this.board[row + 2][col].color
          ) {
            // Очистка ряда
            updatedBoard[row][col].color = "";
            updatedBoard[row + 1][col].color = "";
            updatedBoard[row + 2][col].color = "";
          }
        }
      }

      // Обновление доски
      this.board = updatedBoard;

      // Применяем падение шариков
      this.dropBalls();

      // Генерация новых шариков сверху
      this.generateNewBalls();
    },
    dropBalls() {
      // Падение шариков
      for (let col = 0; col < this.boardSize; col++) {
        let emptySpaces = 0;

        // Идем снизу вверх и двигаем все фишки вниз
        for (let row = this.boardSize - 1; row >= 0; row--) {
          if (this.board[row][col].color === "") {
            emptySpaces++;
          } else if (emptySpaces > 0) {
            // Перемещаем фишку вниз
            this.board[row + emptySpaces][col].color = this.board[row][col].color;
            this.board[row][col].color = "";
          }
        }
      }
    },
    generateNewBalls() {
      // Генерация новых фишек сверху
      const colors = [
        "#FF0000",
        "#024217",
        "#0000FF",
        "#FFFF00",
        "#FF00FF",
        "#00FFFF",
        "#a87532",
        "#28fa6e"
      ];

      for (let col = 0; col < this.boardSize; col++) {
        for (let row = 0; row < this.boardSize; row++) {
          if (this.board[row][col].color === "") {
            // Генерируем случайный цвет для пустых ячеек
            this.board[row][col].color = colors[Math.floor(Math.random() * colors.length)];
          }
        }
      }
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
