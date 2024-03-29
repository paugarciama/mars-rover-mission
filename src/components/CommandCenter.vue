<template>

  <div class="step-one-wrapper" v-if="isStep1">
    <form class="form-container" @submit.prevent="startMission(), updateStep()">
      
      <!-- Input for initial Rover coordinates -->
      <div class="coordinates-container">
        <div>
          <label for="x-axis">X: </label>
          <input 
            v-model="position.x"
            type="number"
            min="0" 
            max="19"
            id="x-axis" 
            name="x-axis" 
            placeholder="0-19"
            required>
        </div>
        <div>
          <label for="y-axis">Y: </label>
          <input
            v-model="position.y" 
            type="number" 
            min="0" 
            max="19"
            id="y-axis" 
            name="y-axis" 
            placeholder="0-19"
            required>
        </div>
      </div>

      <!-- Input for Rover orientation -->
      <div class="directions-container" >
        <div v-for="direction in directions" :key="direction.command">
          <label :for="direction.command">{{ direction.face }}</label>
          <input 
            v-model="orientation" 
            type="radio"
            name="orientation"
            :id="direction.face"
            :value="direction.command"
            required>
        </div>
      </div>

      <button class="start-btn">Start mission</button>

    </form>
  </div>

  <div class="step-two-wrapper" v-if="isStep2">
    <div class="instructions-container" >
      <div 
        v-for="instruction in instructions" 
        :key="instruction.command">
        <button 
          class="instruction-btn"
          @click="sendInstructions(instruction.command)">
          {{ instruction.move }}
        </button>
      </div>
    </div>
  </div>
  
</template>

<script>
import { ref } from '@vue/reactivity'

export default {
  props: {
    position: Object,
    orientation: String,
    instruction: String,
    instructionsCount: Number,
    missionStarted: Boolean
  },
  emits: [ 
    'update:position', 
    'update:orientation', 
    'update:instruction', 
    'update:instructionsCount',
    'update:missionStarted'
  ],
  setup(props, { emit }) {
    const isStep1 = ref(true)
    const isStep2 = ref(false)

    const directions = [
      { face: 'North', command: 'N' },
      { face: 'East', command: 'E' },
      { face: 'South', command: 'S' },
      { face: 'West', command: 'W' }
    ]
    const instructions = [
      { move: 'Left', command: "L" },
      { move: 'Front', command: "F" },
      { move: 'Back', command: "B" },
      { move: 'Right', command: "R" }
    ]

    const startMission = () => {
      emit('update:position', props.position)
      emit('update:orientation', props.orientation)
      emit('update:missionStarted', true)
    }
    const sendInstructions = (command) => {
      emit('update:instruction', command)
      emit('update:instructionsCount', props.instructionsCount += 1)
    }

    const updateStep = () => { 
      isStep1.value = false
      isStep2.value = true 
    }

    return { 
      isStep1,
      isStep2,
      directions,
      instructions,
      startMission,
      sendInstructions,
      updateStep
    }
  }
}
</script>

<style scoped>
.step-one-wrapper,
.step-two-wrapper {
  width: 100%;
  margin: 0 auto;
}
.step-two-wrapper {
  margin-top: -3rem;
}
.form-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.coordinates-container,
.directions-container,
.instructions-container {
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  margin: 1rem 0;
}
.coordinates-container input {
  font-size: 1rem;
  height: 1.5rem;
  width: 3rem;
  border: 0.1rem solid rgb(226, 140, 90);
  border-radius: 0.2rem;
}
.coordinates-container label,
.directions-container label {
  font-size: 0.9rem;
}
.start-btn,
.instruction-btn {
  font-size: 1rem;
  color: #151e27;
  font-weight: 500;
  width: 10rem;
  height: 2.5rem;
  margin: 1rem 0;
  background-color: rgb(226, 140, 90);
  box-shadow: 0.1rem 0.1rem 0.1rem rgb(202, 192, 187);
  border: none;
  border-radius: 0.2rem;
  transition: 0.3s;
}
.instruction-btn {
  font-size: 0.9rem;
  font-weight: 600;
  color: white;
  background-color: rgb(128, 51, 16);
}
.start-btn:hover,
.instruction-btn:hover {
  font-weight: 600;
  color: white;
  background-color: #2c3e50;
  box-shadow: 0.1rem 0.1rem 0.1rem #97a6b4;
  cursor: pointer;
  transform: translateY(-0.2rem);
  transition: 0.25s;
}
</style>