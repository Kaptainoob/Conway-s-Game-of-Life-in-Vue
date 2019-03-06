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
            type="range" min="4" max="48"
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
            type="range" min="0.1" max="2" step="0.1"
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
      <div class="board">
        <div v-for="(row, index) in currentGeneration" :key="index" class="row">
          <div v-for="(cell, index) in row" :key="index"
            :class="cell.isAlive ? 'alive' : 'dead'"
            v-bind:style="`${cellStyles}; ${cellAnimationDurationStyle}; ${isEditable? 'cursor: pointer;' : null}`"
            @click="isEditable ? changeCellLiveState(cell.row, cell.column) : null"  
          ></div>
        </div>
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
      isTimerRunning: false,
      generationCounter: 0,
      isEditable: true,
      cellStyles: null
    };
  },
  computed: {
    cellAnimationDurationStyle() {
      if (this.isEditable) {
        return 'animation-duration: var(--animation-duration)'
      } else {
        return `animation-duration: ${this.timerSpeed / 3}s`
      }
    }
  },
  methods: {
    calculateCellStyles() {
      const basicSize = 1.25
      const basicMargin = 0.25
      let multiplier = 8 / this.size
      return `
        width: ${basicSize * multiplier}rem;
        height: ${basicSize * multiplier}rem;
        margin: ${basicMargin * multiplier}rem;
        box-shadow: 0 ${basicMargin * multiplier}rem ${3 * basicMargin * multiplier}rem 0 var(--box-shadow-color)
      `
    },    
    createBoard() {
      this.cellStyles = this.calculateCellStyles()
      const cells = []
      for (let r = 0; r < this.size; r++) {
        const row = [];
        for (let c = 0; c < this.size; c++) {
          const isAlive = Math.random() <= this.liveProbability ? true : false
          const cell = { row: r, column: c, isAlive: isAlive, liveNeighbours: null }
          row.push(cell)
        }
        cells.push(row)
      }
      this.currentGeneration = cells
      this.generationCounter = 0
      console.log(this.currentGeneration)
    },    
    clearBoard() {
      this.currentGeneration.forEach(row => {
        row.forEach(cell => {
          cell.isAlive = false
        })        
      })
      this.generationCounter = 0
    },
    changeCellLiveState(rowIndex, columnIndex) {
      const certainCell = this.findCell(rowIndex, columnIndex)
      const isAlive = certainCell.isAlive
      certainCell.isAlive = !isAlive
    },
    findCell(rowIndex, columnIndex) {
      return this.currentGeneration[rowIndex][columnIndex]
    }, 
    calculateLiveNeigboursToOneCell(rowIndex, columnIndex) {
      let number = 0
      const neighboursCooridinates = [
        { r: -1, c: 0 }, { r: -1, c: 1 }, { r: 0, c: 1 }, { r: 1, c: 1 },
        { r: 1, c: 0 }, { r: 1, c: -1 }, { r: 0, c: -1 }, { r: -1, c: -1 }
      ]
      neighboursCooridinates.forEach((neighbourCooridinates) => {
        const getIndex = (i) => {
          if (i < 0) {
            return this.size - 1
          } else if (i > this.size - 1) {
            return 0
          } else {
            return i
          }
        }
        const row = getIndex(rowIndex + neighbourCooridinates.r)
        const column = getIndex(columnIndex + neighbourCooridinates.c)
        const isCellAlive = this.findCell(row, column).isAlive
        if (isCellAlive) {
          number++
        }        
      })
      return number
    },
    calculateLiveNeighboursToAllCells() {
      this.currentGeneration.forEach(row => {
        row.forEach(cell => {
          cell.liveNeighbours = this.calculateLiveNeigboursToOneCell(cell.row, cell.column)
        })       
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
      let stateChanges = 0
      this.currentGeneration.forEach(currentRow => {
        const nextRow = []
        currentRow.forEach(currentCell => {
          const isAlive = this.calculateNextLiveState(currentCell.isAlive, currentCell.liveNeighbours)
          const nextCell = { row: currentCell.row, column: currentCell.column, isAlive: isAlive, liveNeighbours: null }
          nextRow.push(nextCell)
          if (isAlive) {
            cellsAlive++
          }
          if (currentCell.isAlive != nextCell.isAlive) {
            stateChanges++
          }          
        })
        nextGeneration.push(nextRow)
      })
      this.currentGeneration = nextGeneration
      this.generetionCounter = this.generationCounter++
      if (cellsAlive == 0 || stateChanges == 0) {
        this.stopTimer()
      }
    },
    startTimer() {
      this.isTimerRunning = true
      this.isEditable = false
      this.tickTock()
    },
    tickTock() {
      if (this.isTimerRunning) {
        this.createNextGeneration()
        setTimeout(() => {
          this.tickTock()
        }, this.timerSpeed * 1000)
      }
    },
    stopTimer() {
      this.isTimerRunning = false
      this.isEditable = true
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
    padding: 1.2rem 0.8rem 1.2rem 0.8rem;
  }

  .container > div {
    margin: 0 0.2rem 0 0.2rem;
  } 

  .column-container {
    display: flex;
    flex-direction: column;
    justify-content: center;  
    padding: 0 0.4rem 0 0.4em  
  }
  
  .column-container > div {
    margin: 0.2rem 0 0.2rem 0;
  }

  .title {
    font-size: 2rem;
    font-weight: bold;
    font-style: italic;
    margin: 1rem 0 1rem 0;
  }

  .value {
    font-size: 2rem;
  }

  .board {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  .row {
    display: flex;
    flex-direction: row;
    justify-content: center;
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
