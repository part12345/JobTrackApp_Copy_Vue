<script setup>
import { reactive, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

const error = ref('')

const applications = JSON.parse(
  localStorage.getItem('jobtrack_applications') || '[]'
)

const existing = applications.find(
  app => String(app.id) === String(route.params.id)
)

if (!existing) {
  router.push('/applications')
}

const form = reactive({
  ...existing
})

function updateApplication() {

  if (
    !form.company ||
    !form.position ||
    !form.location ||
    !form.jobType ||
    !form.dateApplied ||
    !form.status
  ) {
    error.value =
      'Please complete all required fields.'
    return
  }

  const index = applications.findIndex(
    app => app.id === existing.id
  )

  applications[index] = {
    ...form
  }

  localStorage.setItem(
    'jobtrack_applications',
    JSON.stringify(applications)
  )

  router.push(`/applications/${form.id}`)
}
</script>

<template>
  <div class="page">

    <header class="form-header">

      <RouterLink
        :to="`/applications/${form.id}`"
        class="back-button"
      >
        ‹
      </RouterLink>

      <h1>Edit Application</h1>

    </header>

    <div class="form-page">

      <div
        v-if="error"
        class="form-error"
      >
        {{ error }}
      </div>

      <form @submit.prevent="updateApplication">

        <div class="form-group">
          <label>Company Name <span>*</span></label>

          <input v-model="form.company">
        </div>

        <div class="form-group">
          <label>Job Position <span>*</span></label>

          <input v-model="form.position">
        </div>

        <div class="form-group">
          <label>Location <span>*</span></label>

          <input v-model="form.location">
        </div>

        <div class="form-group">
          <label>Job Type <span>*</span></label>

          <select v-model="form.jobType">
            <option>Full-time</option>
            <option>Part-time</option>
            <option>Contract</option>
            <option>Internship</option>
            <option>Freelance</option>
          </select>
        </div>

        <div class="form-group">
          <label>Date Applied <span>*</span></label>

          <input
            v-model="form.dateApplied"
            type="date"
          >
        </div>

        <div class="form-group">
          <label>Status <span>*</span></label>

          <select v-model="form.status">
            <option>Applied</option>
            <option>In Review</option>
            <option>Interview</option>
            <option>Offer</option>
            <option>Rejected</option>
          </select>
        </div>

        <div class="form-group">
          <label>Expected Salary</label>

          <input v-model="form.salary">
        </div>

        <div class="form-group">
          <label>Job Link <small>(optional)</small></label>

          <input v-model="form.jobLink">
        </div>

        <div class="form-group">
          <label>Notes <small>(optional)</small></label>

          <textarea v-model="form.notes"></textarea>
        </div>

        <div class="form-actions">

          <RouterLink
            :to="`/applications/${form.id}`"
            class="secondary-button"
          >
            Cancel
          </RouterLink>

          <button
            type="submit"
            class="primary-button"
          >
            Update Application
          </button>

        </div>

      </form>

    </div>

  </div>
</template>