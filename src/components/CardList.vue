<script>
import axios from 'axios';

export default {
  data() {
    return {
      cards: [],
      archetypes: [], // Memorizza la lista degli archetipi
      selectedArchetype: '', // Contiene l'archetipo selezionato
    };
  },
  methods: {
    async fetchArchetypes() {
      try {
        const response = await axios.get('https://db.ygoprodeck.com/api/v7/archetypes.php');
        this.archetypes = response.data.map(archetype => archetype.archetype_name);
      } catch (error) {
        console.error('Errore nel caricamento degli archetipi:', error);
      }
    },
    async fetchCardsByArchetype() {
      if (this.selectedArchetype) {
        try {
          const response = await axios.get(`https://db.ygoprodeck.com/api/v7/cardinfo.php?archetype=${this.selectedArchetype}`);
          this.cards = response.data.data;
        } catch (error) {
          console.error('Errore nel caricamento delle carte:', error);
        }
      }
    },
  },
  mounted() {
    this.fetchArchetypes();
  },
};
</script>

<template>
  <div class="container">
    <!-- Dropdown filtro archetipi -->
    <div class="text-center mb-4">
      <label for="archetypeFilter" class="form-label">Scegli un archetipo:</label>
      <select id="archetypeFilter" v-model="selectedArchetype" @change="fetchCardsByArchetype" class="form-select w-auto mx-auto">
        <option v-for="archetype in archetypes" :key="archetype" :value="archetype">{{ archetype }}</option>
      </select>
    </div>

    <!-- Layout a griglia delle carte -->
    <div class="row row-cols-1 row-cols-sm-2 row-cols-md-4 g-4">
      <div v-for="card in cards" :key="card.id" class="col">
        <div class="card h-100 shadow-sm">
          <img :src="card.card_images[0].image_url" class="card-img-top img-fluid" alt="Immagine carta" />
          <div class="card-body text-center">
            <h6 class="card-title mb-2">{{ card.name }}</h6>
            <p class="card-text text-muted">{{ card.type }}</p>
          </div>
        </div>
      </div>
    </div>
    <!-- Numero di carte trovate -->
    <h3 class="text-center mb-4">Trovate {{ cards.length }} carte</h3>
  </div>
</template>

<style scoped>
/* Imposta l'altezza fissa per le immagini delle carte */
.card-img-top {
  height: 200px;
  object-fit: cover;
}

/* Riduci la dimensione del titolo delle carte */
.card-title {
  font-size: 1rem;
  font-weight: bold;
}

/* Aggiungi spaziatura tra il titolo e il testo */
.card-text {
  margin-top: 10px;
}

/* Dropdown centrato */
.form-select {
  max-width: 200px;
  margin-bottom: 20px;
}

/* Ombreggiatura leggera sulle carte */
.card {
  transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
}

.card:hover {
  transform: scale(1.05);
  box-shadow: 0px 4px 20px rgba(0, 0, 0, 0.1);
}
</style>