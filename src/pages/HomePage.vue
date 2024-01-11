<template>
<div class="grid">
  <button :title="`move to page ${currentPage -1}`" @click="changePage(currentPage -1)" :disabled="currentPage == 1" class="btn text-dark"><i class="mdi mdi-arrow-left"></i></button>
  <section class="columns my-2">

    <div class="art" v-for="art in artworks" :key="art.id">
      <ArtworkCard  :artwork="art"/>
    </div>

  </section>
  <button :title="`move to page ${currentPage +1}`" @click="changePage(currentPage +1)" :disabled="currentPage == totalPages" class="btn text-dark"><i class="mdi mdi-arrow-right"></i></button>
</div>
<section class="page-counter">
  <kbd class=" text-light rounded-pill px-3">
    {{ currentPage }} / {{ totalPages }}
  </kbd>
</section>
<ActiveArtModal/>
</template>

<script>
import { computed, onMounted } from 'vue';
import ArtworkCard from '../components/ArtworkCard.vue';
import { AppState } from '../AppState.js';
import Pop from '../utils/Pop.js';
import { artworksService } from '../services/ArtworksService.js';
import ActiveArtModal from '../components/ActiveArtModal.vue';

export default {
    setup() {

      onMounted(()=>{
        getArtworks()
      })
      async function getArtworks(){
        try {
          await artworksService.getArtworks()
        } catch (error) {
          Pop.error(error)
        }
      }
        return {
          artworks: computed(()=> AppState.artworks),
          currentPage: computed(()=> AppState.currentPage),
          totalPages: computed(()=> AppState.totalPages),
          async changePage(pageNumber){
            try {
              await artworksService.changePage(pageNumber)
            } catch (error) {
              Pop.error(error)
            }
          }
        };
    },
    components: { ArtworkCard, ActiveArtModal }
}
</script>

<style scoped lang="scss">

.grid{
  display: grid;
  grid-template-columns: 50px 1fr 50px;

  button{
    height: 100dvh;
    position: sticky;
    top: 0;
    font-size: 20px;
    border:0;
    &:disabled{
      color: rgb(168, 175, 184) !important;
    }
  }
}

$gap : 8px;
.columns{
  columns: 300px;
  column-gap: $gap;
  .art{
    margin-bottom: $gap;
  }
}

.page-counter{
  position: fixed;
  bottom: 0;
  display: flex;
  justify-content: center;
  width: 100%;
  padding-bottom: .75em;
  kbd{
    background-color: rgba(0, 0, 0, 0.469);
    backdrop-filter: blur(15px);
  }
}
</style>
