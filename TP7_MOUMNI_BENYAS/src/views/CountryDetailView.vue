<template>
  <h1>Explorateur de pays</h1>

  <p class="loading" v-show="loading">Chargement des pays...</p>
  <p class="error" v-if="error">{{ error }}</p>

  <div class="toolbar">
    <input v-model="recherche" placeholder="Rechercher un pays..." />
  </div>

  <div class="grid">
    <CountryCard
      v-for="country in paysAffiches"
      :key="country.cca3"
      :country="country"
    />
  </div>
</template>

<script>
import CountryCard from '../components/CountryCard.vue'

const API_URL =
  'https://countries.dev/countries?fields=name,alpha3Code,capital,region,population,flags'

export default {
  name: 'CountryListView',

  components: {
    CountryCard
  },

  data() {
    return {
      countries: [],
      loading: true,
      error: null,
      recherche: ''
    }
  },

  computed: {
    paysAffiches() {
      const texte = this.recherche.trim().toLowerCase()

      return this.countries.filter((country) =>
        country.name.common.toLowerCase().includes(texte)
      )
    }
  },

  methods: {
    async chargerPays() {
      this.loading = true
      this.error = null

      try {
        const reponse = await fetch(API_URL)

        if (!reponse.ok) {
          throw new Error(`Erreur HTTP ${reponse.status}`)
        }

        const donnees = await reponse.json()

        this.countries = donnees.map((pays) => ({
          cca3: pays.alpha3Code,
          name: {
            common: pays.name,
            official: pays.name
          },
          capital: pays.capital ? [pays.capital] : [],
          region: pays.region,
          population: pays.population ?? 0,
          flags: pays.flags
        }))
      } catch (e) {
        console.error('Erreur API :', e)
        this.error = `Impossible de charger les pays : ${e.message}`
      } finally {
        this.loading = false
      }
    }
  },

  mounted() {
    this.chargerPays()
  }
}
</script>