<template>
  <main class="page-shell detail-shell">
    <router-link class="back-link" to="/">
      ← Retour à l’annuaire
    </router-link>

    <section v-if="student" class="panel detail-panel">
      <p class="eyebrow">FICHE ÉTUDIANT</p>

      <h1>{{ student.firstName }} {{ student.lastName }}</h1>

      <p class="subtitle">
        Modifiez les informations ci-dessous. Les changements sont partagés avec la liste.
      </p>

      <div class="detail-fields">
        <label>
          Filière
          <input v-model="student.filiere" type="text" />
        </label>

        <label>
          Année
          <select v-model.number="student.year">
            <option :value="1">1ère année</option>
            <option :value="2">2ème année</option>
            <option :value="3">3ème année</option>
            <option :value="4">4ème année</option>
            <option :value="5">5ème année</option>
          </select>
        </label>

        <label>
          Email
          <input
            v-model="student.email"
            type="email"
            placeholder="etudiant@ensa.ma"
          />
        </label>
      </div>

      <p class="save-hint">
        Les modifications sont enregistrées automatiquement.
      </p>
    </section>

    <section v-else class="panel empty-state">
      <h1>Étudiant introuvable</h1>
      <p>Cette fiche n’existe pas dans l’annuaire.</p>

      <router-link class="primary-button link-button" to="/">
        Retour à l’annuaire
      </router-link>
    </section>
  </main>
</template>

<script>
import { store } from '../store/students.js'

export default {
  name: 'StudentDetailView',

  computed: {
    student() {
      const id = Number(this.$route.params.id)
      return store.students.find(item => item.id === id)
    }
  }
}
</script>