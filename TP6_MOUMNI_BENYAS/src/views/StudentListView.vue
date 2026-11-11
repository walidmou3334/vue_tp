<template>
  <main class="page-shell">
    <header class="page-heading">
      <div>
        <p class="eyebrow">ESPACE ÉTUDIANT</p>
        <h1>Annuaire des étudiants</h1>
        <p class="subtitle">
          Retrouvez les étudiants et consultez leurs fiches.
        </p>
      </div>

      <span class="count-pill">
        {{ store.students.length }} étudiant{{ store.students.length > 1 ? 's' : '' }}
      </span>
    </header>

    <section class="panel add-panel">
      <h2>Ajouter un étudiant</h2>

      <form @submit.prevent="ajouterEtudiant">
        <label>
          Prénom
          <input
            v-model.trim="newStudent.firstName"
            type="text"
            placeholder="Ex. Yasmine"
            required
          />
        </label>

        <label>
          Nom
          <input
            v-model.trim="newStudent.lastName"
            type="text"
            placeholder="Ex. Alaoui"
            required
          />
        </label>

        <label>
          Filière
          <input
            v-model.trim="newStudent.filiere"
            type="text"
            placeholder="Ex. Génie Informatique"
          />
        </label>

        <label>
          Année
          <select v-model.number="newStudent.year">
            <option :value="1">1ère année</option>
            <option :value="2">2ème année</option>
            <option :value="3">3ème année</option>
            <option :value="4">4ème année</option>
            <option :value="5">5ème année</option>
          </select>
        </label>

        <label class="email-field">
          Email
          <input
            v-model.trim="newStudent.email"
            type="email"
            placeholder="etudiant@ensa.ma"
          />
        </label>

        <button class="primary-button" type="submit">
          Ajouter l’étudiant
        </button>
      </form>

      <p v-if="messageErreur" class="form-error" role="alert">
        {{ messageErreur }}
      </p>
    </section>

    <section class="directory-section">
      <div class="section-heading">
        <div>
          <h2>Liste des étudiants</h2>
          <p class="muted">
            {{ etudiantsAffiches.length }}
            résultat{{ etudiantsAffiches.length > 1 ? 's' : '' }}
          </p>
        </div>
      </div>

      <div class="toolbar">
        <label class="search-field">
          <span aria-hidden="true">⌕</span>
          <input
            v-model.trim="recherche"
            type="search"
            placeholder="Rechercher par nom ou prénom..."
            aria-label="Rechercher par nom ou prénom"
          />
        </label>

        <label class="sort-field">
          Trier par
          <select v-model="tri">
            <option value="lastName">Nom</option>
            <option value="filiere">Filière</option>
          </select>
        </label>
      </div>

      <div v-if="etudiantsAffiches.length" class="student-list">
        <StudentCard
          v-for="student in etudiantsAffiches"
          :key="student.id"
          :student="student"
        />
      </div>

      <div v-else class="empty-state">
        <h3>Aucun étudiant trouvé</h3>
        <p>
          Essayez une autre recherche ou ajoutez un étudiant à l’annuaire.
        </p>
      </div>
    </section>
  </main>
</template>

<script>
import StudentCard from '../components/StudentCard.vue'
import { store } from '../store/students.js'

export default {
  name: 'StudentListView',

  components: {
    StudentCard
  },

  data() {
    return {
      store,
      recherche: '',
      tri: 'lastName',
      messageErreur: '',
      newStudent: {
        firstName: '',
        lastName: '',
        filiere: '',
        year: 1,
        email: ''
      }
    }
  },

  computed: {
    etudiantsAffiches() {
      const texte = this.recherche.toLocaleLowerCase('fr').trim()

      return this.store.students
        .filter(student =>
          `${student.firstName} ${student.lastName}`
            .toLocaleLowerCase('fr')
            .includes(texte)
        )
        .slice()
        .sort((a, b) =>
          String(a[this.tri]).localeCompare(
            String(b[this.tri]),
            'fr',
            { sensitivity: 'base' }
          )
        )
    }
  },

  methods: {
    ajouterEtudiant() {
      const firstName = this.newStudent.firstName.trim()
      const lastName = this.newStudent.lastName.trim()

      if (!firstName || !lastName) {
        this.messageErreur = 'Le prénom et le nom sont obligatoires.'
        return
      }

      const id = this.store.students.length
        ? Math.max(...this.store.students.map(student => Number(student.id))) + 1
        : 1

      this.store.students.push({
        id,
        firstName,
        lastName,
        filiere: this.newStudent.filiere.trim() || 'Filière non renseignée',
        year: Number(this.newStudent.year) || 1,
        email: this.newStudent.email.trim()
      })

      this.newStudent = {
        firstName: '',
        lastName: '',
        filiere: '',
        year: 1,
        email: ''
      }

      this.messageErreur = ''
    }
  }
}
</script>