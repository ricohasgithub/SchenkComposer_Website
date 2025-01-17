<template>
  <div>
    <b-container class="text-center">
      <b-row class="my-4 myRow">
        <b-col></b-col>
        <b-col>
          <b-button
              variant="success"
              id="ps"
              @click="redirect('/phrase-structure')"
          >
          <span style="font-size:0.6em;">Phrase</span><br>
          <span style="font-size:0.6em;">Structure</span>
          </b-button>
        </b-col>
        <b-col></b-col>
      </b-row>
      <b-row class="my-4 myRow">
        <b-col>
          <b-button
              variant="danger"
              id="mh"
              @click="redirect('/meter-hypermeter')"
          >
          <span style="font-size:0.6em;">Meter and</span> <br>
          <span style="font-size:0.6em;">Hypermeter</span>
          </b-button>
        </b-col>
        <b-col>
          <b-button
              variant="danger"
              id="mghr"
              @click="redirect('/mg-harmonic-dagRhythm')"
          >
          <span style="font-size:0.6em;">Harmonic</span><br>
          <span style="font-size:0.6em;">Rhythm</span>
          </b-button>
        </b-col>
        <b-col>
          <b-button
              variant="info"
              id="mghp"
              @click="redirect('/harmonic-progression')"
          >
          <span style="font-size:0.6em;">Harmonic Progression</span>
          </b-button>
        </b-col>
      </b-row>
      <b-row class="my-4 myRow">
        <b-col></b-col>
        <b-col></b-col>
        <b-col>
          <b-button
              variant="warning"
              id="mgm"
              @click="redirect('/middleground-melody')"
          ><span style="font-size:0.6em;">Middleground Melody</span></b-button>
        </b-col>
      </b-row>
      <b-row class="my-4 myRow">
        <b-col></b-col>
        <b-col>
          <b-button
              variant="danger"
              id="fgmr"
              @click="redirect('/foreground-rhythm')"
          ><span style="font-size:0.6em;">Melodic</span><br><span style="font-size:0.6em;">Rhythm</span></b-button>
        </b-col>
        <b-col>
          <b-button
              variant="warning"
              id="fgm"
              @click="redirect('/foreground-melody')"
          ><span style="font-size:0.6em;">Foreground Melody</span></b-button>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script setup lang="ts">
import LeaderLine, { SocketType } from "leader-line-new"

import {router} from '@/main'
import {onMounted, Ref, ref, watch} from "vue";

let ps_mh: LeaderLine;
let ps_mghr: LeaderLine;
let ps_mghp: LeaderLine;
let mh_mghr: LeaderLine;
let mh_fgmr: LeaderLine;
let mghr_fgmr: LeaderLine;
let mghr_mghp: LeaderLine;
let mghr_mgm: LeaderLine;
let mghp_mgm: LeaderLine;
let mgm_fgm: LeaderLine;
let fgmr_fgm: LeaderLine;

interface Props {
  phraseAnim?: boolean,
  meterAnim?: boolean,
  mgrhythmAnim?: boolean,
  mgharmonyAnim?: boolean,
  mgmelodyAnim?: boolean,
  fgrhythmAnim?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  phraseAnim: false,
  meterAnim: false,
  mgrhythmAnim: false,
  mgharmonyAnim: false,
  mgmelodyAnim: false,
  fgrhythmAnim: false
})

watch(() => props.phraseAnim, () => {
  activateAnimation(['psMh', 'psMghr', 'psMghp'], props.phraseAnim)
})
watch(() => props.meterAnim, () => {
  activateAnimation(['mhMghr', 'mhFgmr'], props.meterAnim)
})
watch(() => props.mgrhythmAnim, () => {
  activateAnimation(['mghrFgmr', 'mghrMghp', 'mghrMgm'], props.mgrhythmAnim)
})
watch(() => props.mgharmonyAnim, () => {
  activateAnimation(['mghpMgm'], props.mgharmonyAnim)
})
watch(() => props.mgmelodyAnim, () => {
  activateAnimation(['mgmFgm'], props.mgmelodyAnim)
})
watch(() => props.fgrhythmAnim, () => {
  activateAnimation(['fgmrFgm'], props.fgrhythmAnim)
})

let psMh = ref(false);
let psMghr = ref(false);
let psMghp = ref(false);
let mhMghr = ref(false);
let mhFgmr = ref(false);
let mghrFgmr = ref(false);
let mghrMghp = ref(false);
let mghrMgm = ref(false);
let mghpMgm = ref(false);
let mgmFgm = ref(false);
let fgmrFgm = ref(false);

let psDiv: Element;
let mhDiv: Element;
let mghrDiv: Element;
let mgmDiv: Element;
let mghpDiv: Element;
let fgmDiv: Element;
let fgmrDiv: Element;

function redirect(path:string) {
  router.push({path: path})
}

onMounted(() => {
  psDiv = document.getElementById("ps") as Element
  mhDiv = document.getElementById("mh") as Element
  mghrDiv = document.getElementById("mghr") as Element
  mgmDiv = document.getElementById("mgm") as Element
  mghpDiv = document.getElementById("mghp") as Element
  fgmDiv = document.getElementById("fgm") as Element
  fgmrDiv = document.getElementById("fgmr") as Element
  drawLines()
})

// HELPERS

function createLine(div1: Element, div2: Element, start: SocketType, end: SocketType, gravity: number[], animated: boolean) {
  return new LeaderLine(
      div1,
      div2,
      {
        path: 'magnet',
        startSocket: start,
        endSocket: end,
        startSocketGravity: gravity,
        dash: {animation: animated},
        size: 3,
        color: animated ? 'green': 'red'
      }
  )
}

function drawLines() {
  //From phrase structure
  ps_mh = createLine(psDiv, mhDiv, 'left', 'top', [-150, 0], psMh.value)
  ps_mghr = createLine(psDiv, mghrDiv, 'bottom', 'top', [0, 0], psMghr.value)
  ps_mghp = createLine(psDiv, mghpDiv, 'right', 'top', [150, 0], psMghp.value)
  //From Meter and Hypermeter
  mh_mghr = createLine(mhDiv, mghrDiv, 'right', 'left', [0, 0], mhMghr.value)
  mh_fgmr = createLine(mhDiv, fgmrDiv, 'bottom','left', [0, 190], mhFgmr.value)
  //From Middleground Harmonic Rhythm
  mghr_fgmr = createLine(mghrDiv, fgmrDiv, 'bottom', 'top', [0, 0], mghrFgmr.value)
  mghr_mghp = createLine(mghrDiv, mghpDiv, 'right', 'left', [0, 0], mghrMghp.value)
  mghr_mgm = createLine(mghrDiv, mgmDiv, 'bottom', 'left', [50, 70], mghrMgm.value)
  //From Middleground Harmonic Progression
  mghp_mgm = createLine(mghpDiv, mgmDiv, 'bottom', 'top', [0, 0], mghpMgm.value)
  //From Middleground Melody
  mgm_fgm = createLine(mgmDiv, fgmDiv, 'bottom', 'top', [0, 0], mgmFgm.value)
  //From Foreground Melodic Rhythm
  fgmr_fgm = createLine(fgmrDiv, fgmDiv, 'right','left',[0, 0], fgmrFgm.value)

  console.log(ps_mh);

  document.addEventListener('scroll', function() {
    repositionLines();
  }, false)

}

function repositionLines() {
    ps_mh.position();
    ps_mghr.position();
    ps_mghp.position();
    mh_mghr.position();
    mh_fgmr.position();
    mghr_fgmr.position();
    mghr_mghp.position();
    mghr_mgm.position();
    mghp_mgm.position();
    mgm_fgm.position();
    fgmr_fgm.position();
}

function activateAnimation(refNames: string[], activate: boolean = true) {
  for (let refName of refNames) {
    let reference: Ref<boolean>;
    let line: LeaderLine;
    switch (refName) {
      case 'psMh': reference = psMh; line = ps_mh; break;
      case 'psMghr': reference = psMghr; line = ps_mghr; break;
      case 'psMghp': reference = psMghp; line = ps_mghp; break;
      case 'mhMghr': reference = mhMghr; line = mh_mghr; break;
      case 'mhFgmr': reference = mhFgmr; line = mh_fgmr; break;
      case 'mghrMghp': reference = mghrMghp; line = mghr_mghp; break;
      case 'mghrMgm': reference = mghrMgm; line = mghr_mgm; break;
      case 'mghrFgmr': reference = mghrFgmr; line = mghr_fgmr; break;
      case 'mghpMgm': reference = mghpMgm; line = mghp_mgm; break;
      case 'mgmFgm': reference = mgmFgm; line = mgm_fgm; break;
      case 'fgmrFgm': reference = fgmrFgm; line = fgmr_fgm; break;
      default: return
    }
    reference.value = activate
    line.color = reference.value ? 'green': 'red'
    line.setOptions({dash: {animation: reference.value}})
  }
}

</script>

<style scoped>
.btn {
  border-color: rgba(0,0,0,0);
  font-size: 24px;
}

.myRow {
  width: 400px;
}


</style>