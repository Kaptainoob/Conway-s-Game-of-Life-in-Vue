<template>
  <div class="hello">
    <div class="container">
      <span class="title">Conway's Game of Life</span>
    </div>
    <div class="container">
      <div class="column-container">
        <div>
          <span>Size</span>
        </div>
        <div>
          <span class="value">{{ size }} x {{ size }}</span> 
        </div>
        <div>
          <input :class="isTimerRunning ? 'slider disabled-slider' : 'slider active-slider'"
            type="range" min="4" max="16"
            v-model="size" @change="createBoard" :disabled="isTimerRunning"
          > 
        </div>                          
      </div>
      <div class="column-container">
        <div>
          <span>Live Probability</span>
        </div>
        <div>
          <span class="value">{{ Math.round(liveProbability * 100) }} %</span>
        </div>
        <div>
          <input :class="isTimerRunning ? 'slider disabled-slider' : 'slider active-slider'"
            type="range" min="0" max="1" step="0.01"
            v-model="liveProbability" @change="createBoard" :disabled="isTimerRunning"           
          >  
        </div>           
      </div>
      <div class="column-container">
        <div>
          <span>Speed</span>
        </div>
        <div>
          <span class="value">{{ timerSpeed }} sec</span> 
        </div>
        <div>
          <input :class="isTimerRunning ? 'slider disabled-slider' : 'slider active-slider'"
            type="range" min="0.4" max="2" step="0.1"
            v-model="timerSpeed" :disabled="isTimerRunning"
          > 
        </div>                
      </div>
    </div>
    <div class="container">
      <div v-if="!isTimerRunning">
        <button class="button button-primary" @click="createBoard">Generate</button>
      </div>
      <div v-if="!isTimerRunning">
        <button class="button button-primary" @click="clearBoard">Clear</button>
      </div>
      <div v-if="!isTimerRunning">
        <button class="button button-primary" @click="createNextGeneration">Next</button>
      </div>
      <div v-if="!isTimerRunning">
        <button class="button button-primary" @click="startTimer">Start</button>
      </div>
      <div v-if="isTimerRunning">
        <button class="button button-primary" @click="stopTimer">Stop</button>
      </div>    
    </div>
    <div class="container">
      <span>Generation: <strong>{{ generationCounter }}</strong></span>
    </div>
    <div class="container">
      <div class="board" v-bind:style="`
        grid-template-columns: ${gridTemplateStyle};
        grid-template-rows: ${gridTemplateStyle};
      `">
        <div
          v-for="(cell, index) in currentGeneration"
          :key="index" :class="cell.isAlive ? 'cell alive' : 'cell dead'"
          v-bind:style="`
            grid-column-start: ${cell.column + 1}; 
            grid-column-end: ${cell.column + 1};
            grid-row-start: ${cell.row + 1};
            grid-row-end: ${cell.row + 1};
            animation-duration: ${timerSpeed / 3}s;
            ${isEditable? 'cursor: pointer;' : null}
          `"
          @click="isEditable ? changeCellLiveState(cell.row, cell.column) : null"  
        ></div>
      </div>      
    </div>
    <br>
    <div class="container page-ending">
      <span>More about <a href="https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life">Conway's Game of Life</a></span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      size: 8,
      liveProbability: 0.5,
      timerSpeed: 1,
      currentGeneration: [],
      timer: null,
      isTimerRunning: false,
      generationCounter: 0,
      isEditable: true
    };
  },
  computed: {
    gridTemplateStyle() {
      return `repeat(${Math.sqrt(this.currentGeneration.length)}, 1.0em)`
    }
  },
  methods: {
    createBoard() {
      const cells = [];
      for (let r = 0; r < this.size; r++) {
       for (let c = 0; c < this.size; c++) {
         const isAlive = Math.random() <= this.liveProbability ? true : false
         const cell = { row: r, column: c, isAlive: isAlive, liveNeighbours: null }
         cells.push(cell)
       } 
      }
      this.currentGeneration = cells
      this.generationCounter = 0
    },
    clearBoard() {
      this.currentGeneration.forEach(cell => {
        cell.isAlive = false
      })
      this.generationCounter = 0
    },
    changeCellLiveState(rowIndex, columnIndex) {
      const certainCell = this.findCell(rowIndex, columnIndex)
      const isAlive = certainCell.isAlive
      certainCell.isAlive = !isAlive
    },
    findCell(rowIndex, columnIndex) {
      const certainCell = this.currentGeneration.find(cell => {
        return cell.row == rowIndex && cell.column == columnIndex
      })
      return certainCell
    }, 
    calculateLiveNeigboursToOneCell(rowIndex, columnIndex) {
      let number = 0
      // Top middle
      if (columnIndex > 0) {
        const isCellAlive = this.findCell(rowIndex, columnIndex - 1).isAlive
        if (isCellAlive) {
          number++
        }
      }    
      // Top right
      if (rowIndex < this.size - 1 && columnIndex > 0) {
        const isCellAlive = this.findCell(rowIndex + 1, columnIndex - 1).isAlive
        if (isCellAlive) {
          number++
        }
      }
      // Center right
      if (rowIndex < this.size - 1) {
        const isCellAlive = this.findCell(rowIndex + 1, columnIndex).isAlive
        if (isCellAlive) {
          number++
        }
      }
      // Bottom right
      if (rowIndex < this.size - 1 && columnIndex < this.size - 1) {
        const isCellAlive = this.findCell(rowIndex + 1, columnIndex + 1).isAlive
        if (isCellAlive) {
          number++
        }
      }
      // Bottom middle
      if (columnIndex < this.size - 1) {
        const isCellAlive = this.findCell(rowIndex, columnIndex + 1).isAlive
        if (isCellAlive) {
          number++
        }
      }
      // Bottom left
      if (rowIndex > 0 && columnIndex < this.size - 1) {
        const isCellAlive = this.findCell(rowIndex - 1, columnIndex + 1).isAlive
        if (isCellAlive) {
          number++
        }
      }
      // Center left
      if (rowIndex > 0) {
        const isCellAlive = this.findCell(rowIndex - 1, columnIndex).isAlive
        if (isCellAlive) {
          number++
        }
      }
      // Top left
      if (rowIndex > 0 && columnIndex > 0) {
        const isCellAlive = this.findCell(rowIndex - 1, columnIndex - 1).isAlive
        if (isCellAlive) {
          number++
        }
      }     
      return number      
    },
    calculateLiveNeighboursToAllCells() {
      this.currentGeneration.forEach(cell => {
        cell.liveNeighbours = this.calculateLiveNeigboursToOneCell(cell.row, cell.column)
      })
    },
    calculateNextLiveState(currentLiveState, liveNeighbours) {
      if (currentLiveState) {
        if (liveNeighbours == 2 || liveNeighbours == 3) {
          return true
        } else {
          return false
        }
      } else {
        if (liveNeighbours == 3) {
          return true
        } else {
          return false
        }
      }
    },
    createNextGeneration() {
      this.calculateLiveNeighboursToAllCells()
      const nextGeneration = []
      let cellsAlive = 0
      this.currentGeneration.forEach(currentCell => {
        const isAlive = this.calculateNextLiveState(currentCell.isAlive, currentCell.liveNeighbours)
        const nextCell = { row: currentCell.row, column: currentCell.column, isAlive: isAlive, liveNeighbours: null }
        nextGeneration.push(nextCell)
        if (isAlive) {
          cellsAlive++
        }
      })
      this.currentGeneration = nextGeneration
      this.generetionCounter = this.generationCounter++
      if (cellsAlive == 0) {
        this.stopTimer()
      }
    },
    startTimer() {
      this.isTimerRunning = true
      this.isEditable = false
      this.timer = setInterval(() => {
        this.createNextGeneration()
      }, this.timerSpeed * 1000)
    },
    stopTimer() {
      this.isTimerRunning = false
      this.isEditable = true
      clearInterval(this.timer)
    }
  },
  created() {
    this.createBoard()
  }

}
</script>

<style scoped>
  .container {
    display: flex;
    flex-direction: row;
    justify-content: center;
    padding: 1.2em 0 1.2em 0;
  }

  .container > div {
    margin: 0 0.2em 0 0.2em;
  } 

  .column-container {
    display: flex;
    flex-direction: column;
    justify-content: center;    
  }
  
  .column-container > div {
    margin: 0.2em 0 0.2em 0;
  }

  .board {
    display: grid;
    grid-row-gap: 0.7em;
    grid-column-gap: 0.7em

  }

  .title {
    font-size: 2em;
    font-weight: bold;
    font-style: italic;
  }

  .value {
    font-size: 2em;
  }

  .row {
    display: flex;
    flex-direction: row;
  }

  .cell {
    width: 1.2em;
    height: 1.2em;
    box-shadow: 0em 0.25em 0.75em 0px var(--box-shadow-color);
  }

  .alive {
    background-color: var(--primary-color);
    animation-name: from-dead-to-alive;
  }

  .dead {
    background-color: var(--main-grey);
    animation-name: from-alive-to-dead;
  }

  @keyframes from-dead-to-alive {
    from {background-color: var(--main-grey);}
    to {background-color: var(--primary-color);}
  }

  @keyframes from-alive-to-dead {
    from {background-color: var(--primary-color);}
    to {background-color: var(--main-grey);}    
  }
</style>
