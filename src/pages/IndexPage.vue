<template>
  <q-page class="flex flex-center flex-space-around">
    <div class="row full-container">
      <!-- Cigarette Packet -->
      <div class="bor">
        <div class="top-area">
          <div v-if="!isOpen" class="top">Cigarettes</div>
          <div v-else class="cigarettes">
            <div v-for="n in r" :key="n" class="cigarette"></div>
          </div>
        </div>

        <div class="middle column q-gutter-y-md">
          <div>Remaining: {{ r }}</div>

          <q-btn
            round dense color="warning" size="sm"
            :label="isOpen ? 'Close' : 'Open'"
            @click="toggleOpen"
            style="padding:0.7rem; font-size: 1.2rem;"
          />

          <q-btn
            dense color="secondary" size="md"
            label="Take Cigarette"
            class="take-btn"
            :disabled="!isOpen || r <= 0 || !t"
            @click="takeCig"
          />
        </div>

        <div class="bottom flex flex-center text-dark">
          Smoking is injurious to health
        </div>
      </div>

      <!-- Cigarette Display -->
      <div class="cigarette-container column q-gutter-y-md">
        <div class="flex row">
          <div class="cig" v-if="take">
            <div class="cigg flex row-inline">
              <div class="filter"></div>

              <!-- shrinking body -->
              <div class="body" :style="{ width: currentWidth + 'rem' }"></div>
              <div v-if="f" class="tip"></div>

              <!-- Ash accumulated on cigarette -->
              <div v-if="ashOnCig > 0" class="ash-stack">
                <div
                  v-for="n in ashOnCig"
                  :key="n"
                  class="ash-unit"
                ></div>
              </div>
            </div>
          </div>

          <!-- smoke -->
          <div v-if="smoke">
            <img src="~assets/smoke.png" alt="" style="width:3rem;">
          </div>
        </div>

        <div>
          <q-btn
            dense color="secondary" size="3rem"
            :disabled="fire"
            @click="fireOn" round
          >
            <q-avatar size="5rem">
              <img src="~assets/pyromania.png">
            </q-avatar>
          </q-btn>

          <q-btn
            dense color="warning" size="3rem" style="margin:1rem"
            :disabled="!fire" round
            @click="takePuff"
          >
            <q-avatar size="5rem">
              <img src="~assets/smoking.png">
            </q-avatar>
          </q-btn>

          <q-btn
            round size="lg"
            dense color="negative" 
            class="remove-ash-btn"
            :disabled="ashOnCig === 0 && l !== 0"
            @click="removeAsh"> 
            <q-avatar size="7rem">           
              <img src="~assets/ash-tray.png" style="padding:1rem; font-size: 1.2rem;">
            </q-avatar>
          </q-btn>
        </div>
      </div>

      <!-- Ashtray -->
      <div class="ashtray column flex">
        <div class="ashtray-cup">
          <div
            v-for="(a, i) in ashtrayAsh"
            :key="i"
            :class="[a.type === 'ash' ? 'ash-piece' : 'filter-piece']"
          ></div>
        </div>
        <div class="ashtray-label">Ashtray</div>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { ref, computed } from 'vue'

const r = ref(20)       
const take = ref(false) 
const f = ref(false)    
const t = ref(true)     
const isOpen = ref(false)
const fire = ref(false)
const smoke = ref(false)

const lInitial = 6
const l = ref(lInitial)
const unitWidth = 12 / lInitial
const currentWidth = computed(() => l.value * unitWidth)

// ash tracking
const ashOnCig = ref(0)     // ash units currently on cig
const ashtrayAsh = ref([])  // all ash moved to tray

const toggleOpen = () => {
  isOpen.value = !isOpen.value
  t.value = true
}

const takeCig = () => {
  r.value--
  take.value = true
  t.value = false
  l.value = lInitial
  f.value = false
  ashOnCig.value = 0
  fire.value = false
}

const fireOn = () => {
  f.value = true
  fire.value = true
}

const takePuff = () => {
  if (l.value > 0) {
    smoke.value = true
    setTimeout(() => { smoke.value = false }, 500)

    l.value--
    ashOnCig.value++ // 1 unit ash per puff
  }

  if (l.value === 0) {
    fire.value = false   // no more burning
    f.value = false      // no glowing tip
    // keep cigarette visible until ash removed
  }
}

const removeAsh = () => {
  if (ashOnCig.value > 0) {
    for (let i = 0; i < ashOnCig.value; i++) {
      ashtrayAsh.value.push({ type: 'ash' }) // push → grid auto fills left-to-right
    }
    ashOnCig.value = 0
  }

  // if cigarette finished, also add filter
  if (l.value === 0) {
    ashtrayAsh.value.push({ type: 'filter' })
    take.value = false // remove cigarette from hand
    t.value = true     // allow next cigarette
  }
}
</script>

<style scoped lang="scss">
.full-container {
  width: 100%;
  display: flex;
  align-items: flex-start;
  justify-content: space-around;
}

/* Packet stays fixed left */
.bor {
  position: absolute;
  left: 2rem;
  top: 2rem;

  width: 14rem;
  height: 20rem;
  border: 2px solid #333;
  border-radius: 6px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  background: #f5f5f5;
  box-shadow: 2px 2px 8px rgba(0,0,0,0.2);
}

.top-area {
  height: 5rem;
  display: flex;
  justify-content: center;
  align-items: center;

  .top {
    width: 100%;
    height: 100%;
    background: #444;
    display: flex;
    justify-content: center;
    align-items: center;
    font-weight: bold;
    color: white;
  }

  .cigarettes {
    display: grid;
    grid-template-columns: repeat(10, 1fr);
    grid-gap: 2px;
    width: 90%;

    .cigarette {
      width: 1rem;
      height: 1.5rem;
      background: #65420d;
      border-top: 0.3rem solid #d46b2c;
      border-radius: 2px;
    }
  }
}

.middle {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #7c7a7a;
}

.bottom {
  height: 3rem;
  background: #ccc;
}

.cigarette-container {
  margin-left: 18rem; /* leaves space for fixed packet */
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 20rem;
}

.cig {
  height: 5rem;
  width: 20rem;
}
.cigg {
  display: flex;
  align-items: center;
  height: 5rem;
}

.filter {
  width: 6rem;
  background: #d9a066;
  height: 2rem;
  border-radius: 0.5rem;
}

.body {
  height: 2rem;
  background: #d1cece;
  border-radius: 0.5rem;
  transition: width 0.3s ease;
}

.tip {
  width: 1.5rem;
  background: orange;
  box-shadow: 0 0 6px 2px rgba(255, 165, 0, 0.7);
  height: 2rem;
  border-radius: 0.5rem;
  margin-left: 0.3rem;
}

/* Ash on cigarette */
.ash-stack {
  display: flex;
  flex-direction: row;
  margin-left: 0.3rem;
}
.ash-unit {
  width: 1.5rem;
  height: 2rem;
  background: #555;
  margin: 1px;
  border-radius: 2px;
}

/* Ashtray Cup (grid) */
.ashtray {
  margin-left: 3rem;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.ashtray-cup {
  width: 15rem;
  height: 10rem;
  background: rgba(200,200,200,0.2); /* transparent glass effect */
  border: 3px solid rgba(150,150,150,0.5);
  border-radius: 0 0 25% 25%;
  overflow-y: auto;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(1.5rem, 1fr));
  grid-auto-rows: 2rem;
  gap: 0.3rem;
  justify-items: center;
  align-items: end;
  padding: 0.5rem;
}

.ash-piece {
  width: 1.5rem;
  height: 2rem;
  background: #444;
  border-radius: 2px;
}

.filter-piece {
  width: 1.5rem;
  height: 2rem;
  background: #d9a066;
  border-radius: 2px;
}

.ashtray-label {
  margin-top: 0.5rem;
  font-size: 0.9rem;
  font-weight: bold;
  color: #444;
}
</style>





