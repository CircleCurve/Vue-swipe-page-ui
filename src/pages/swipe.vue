<script setup>
import { computed, ref, watch } from 'vue'

const images = ref([
  { id: 1, name: 'Image 1', href: 'https://cms-assets.tutsplus.com/uploads/users/1191/profiles/19701/profileImage/ngc2237_400.jpg' },
  { id: 2, name: 'Image 2', href: 'https://www.researchgate.net/profile/Gareth-Roberts-3/publication/251839685/figure/fig3/AS:652228557803529@1532514814266/a-A-SEVIRI-400-x-400-pixel-scene-of-southern-Africa-September-4-th-1212pm.png' },
  { id: 3, name: 'Image 3', href: 'https://ww1.prweb.com/prfiles/2005/06/07/249097/dumbbellmed.jpg' },
  { id: 4, name: 'Image 4', href: 'https://pbs.twimg.com/card_img/1578434548177715200/pFmenLG1?format=png&name=small' },
  { id: 5, name: 'Image 5', href: 'http://www.lonestardigital.com/EOS_D30/Sample_Pix/D30_Img_3771(400x400_Crop).jpg' },
  { id: 6, name: 'Image 6', href: 'http://img.cdn.91app.hk/webapi/imagesV3/Original/SalePage/87/0/637626336263800000?v=1' },
  { id: 7, name: 'Image 7', href: 'https://www.filterforge.com/more/help/images/size400.jpg' },
])

const leagues = ref([
  { id: 1, name: 'League 1' },
  { id: 2, name: 'League 2' },
  { id: 3, name: 'League 3' },
  { id: 4, name: 'League 4' },
])

const matches = ref([
  { id: 1, name: 'L1 Test 1 vs Test 2', league: 1 },
  { id: 2, name: 'L1 Test 2 vs Test 3', league: 1 },
  { id: 3, name: 'L1 Test 3 vs Test 4', league: 1 },
  { id: 4, name: 'L1 Test 4 vs Test 5', league: 1 },
  { id: 5, name: 'L1 Test 5 vs Test 6', league: 1 },

  { id: 6, name: 'L2 Test 1 vs Test 2', league: 2 },
  { id: 7, name: 'L2 Test 2 vs Test 3', league: 2 },
  { id: 8, name: 'L2 Test 3 vs Test 4', league: 2 },
  { id: 9, name: 'L2 Test 4 vs Test 5', league: 2 },
  { id: 10, name: 'L2 Test 5 vs Test 6', league: 2 },

  { id: 11, name: 'L3 Test 1 vs Test 2', league: 3 },
  { id: 12, name: 'L3 Test 2 vs Test 3', league: 3 },
  { id: 13, name: 'L3 Test 3 vs Test 4', league: 3 },
  { id: 14, name: 'L3 Test 4 vs Test 5', league: 3 },
  { id: 15, name: 'L3 Test 5 vs Test 6', league: 3 },

  { id: 16, name: 'L4 Test 1 vs Test 2', league: 4 },
  { id: 17, name: 'L4 Test 2 vs Test 3', league: 4 },
  { id: 18, name: 'L4 Test 3 vs Test 4', league: 4 },
  { id: 19, name: 'L4 Test 4 vs Test 5', league: 4 },
  { id: 20, name: 'L4 Test 5 vs Test 6', league: 4 },
])

const matchInLeagues = computed(() => {
  return leagues.value.map(league => (
    {
      leagueId: league.id,
      matches: matches.value.filter(match => match.league === league.id),
    }
  ))
},
)
const slidePosition = ref(0)
const slideToLeft = () => {
  slidePosition.value += 20
}

const slideToRight = () => {
  slidePosition.value -= 20
}
// page related
const pageSettings = ref({
  initalPage: 1,
  currentPage: 1,
  maxPage: 1,
  offset: 0,
  isAnimated: true,
})

watch(matchInLeagues, (val) => {
  pageSettings.value.maxPage = val.length
}, { immediate: true })

const movePercentage = computed(() => (pageSettings.value.initalPage - pageSettings.value.currentPage) * 100 + pageSettings.value.offset)

const movePage = (page) => {
  pageSettings.value.currentPage = page
  pageSettings.value.offset = 0
}

// handle drag

const startDrag = (event) => {
  console.log('onMouseDown :', event)
  const currPos = { x: event.clientX, y: event.clientY, width: event.target.offsetWidth }
  console.log('currPos : ', currPos)
  document.onmousemove = (evt) => {
    evt.preventDefault()
    const offset = evt.clientX - currPos.x
    const direction = offset < 0 ? 'left' : 'right'
    const movePercentage = offset / currPos.width * 100
    pageSettings.value.offset = movePercentage
    pageSettings.value.isAnimated = false
    // console.log('OffetX : ', evt.offsetX, '  CurrPos.X : ', currPos.x, ', NewPos.X :', evt.clientX, '  Move offset :', offset, ' Direction:', direction, ' Move percentage :', movePercentage)
  }
  document.onmouseup = (evt) => {
    document.onmouseup = null
    document.onmousemove = null

    pageSettings.value.isAnimated = true
    const offset = evt.clientX - currPos.x
    const direction = offset < 0 ? 1 : -1
    const movePercentage = Math.abs(offset) / currPos.width * 100

    let nextPage = (movePercentage >= 50) ? pageSettings.value.currentPage + direction : pageSettings.value.currentPage
    nextPage = nextPage < 1 || nextPage > pageSettings.value.maxPage ? pageSettings.value.currentPage : nextPage
    movePage(nextPage)
  }
}
</script>

<template>
  <section class="margin-x-16">
    <div class="w-80 center red-border hidden">
      <div class="flex slide animate-transform" :style="{ transform: `translate(${slidePosition}%)` }">
        <div v-for="image in images" :key="image.id" class="flex-w-20 blue-border">
          <img class="w-100 image-cover" :src="image.href">
        </div>
      </div>
    </div>
    <div class="center w-80 flex justify-content-center">
      <div class="padding-16 cursor green-border" @click="slideToLeft">
        Left
      </div>
      <div class="padding-16 cursor green-border" @click="slideToRight">
        Right
      </div>
    </div>
  </section>

  <section>
    <div class="center w-80 flex justify-content-center">
      <div v-for="league in leagues" :key="league.id" class="padding-16 cursor green-border margin-y-16" @click="movePage(league.id)">
        {{ league.name }}
      </div>
    </div>

    <div class="w-80 center red-border ">
      <div class="flex slide" :class="{ 'animate-transform': pageSettings.isAnimated }" :style="{ transform: `translate(${movePercentage}%)` }">
        <div v-for="matchInLeague in matchInLeagues" :key="matchInLeague.id" class="flex-w-100 blue-border" @mousedown="startDrag($event)">
          <div v-for="match in matchInLeague.matches" :key="match.id">
            {{ match.name }}
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style>
  * {
    box-sizing: border-box ;
    padding : 0 ;
    margin : 0 ;
  }
  .padding-16 {
    padding : 16px ;
  }
  .cursor {
    cursor:pointer
  }
  .center {
    margin: 0 auto ;
  }
  .margin-x-16 {
    margin-top: 1rem;
    margin-bottom: 1rem;
  }
  .margin-y-16 {
    margin-left: 1rem;
    margin-right: 1rem;
  }
  .flex {
    display: flex;
  }
  .flex-w-20 {
    flex: 0 0 20% ;
  }
  .flex-w-100 {
    flex: 0 0 100% ;
  }
  .justify-content-center {
    justify-content: center;
  }
  .hidden {
    overflow: hidden ;
  }
  .w-80 {
    width : 80%;
  }

  .w-100 {
    width : 100%;
    height: 100%;
  }
  .image-cover {
    object-fit: cover;
  }
  .red-border {
    border : 3px solid red;
  }

  .blue-border {
    border: 2px solid blue;
  }

  .green-border {
    border: 2px solid green;
  }

  .slide {
    transform: translateX(0%) ;
  }

  .animate-transform {
    transition: transform 0.3s;
  }
</style>
