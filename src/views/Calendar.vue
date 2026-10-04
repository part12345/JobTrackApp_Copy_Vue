<script setup>
import { computed, ref } from 'vue'

const applications = JSON.parse(
  localStorage.getItem('jobtrack_applications') || '[]'
)

const currentDate = ref(new Date())

const monthName = computed(() => {
  return currentDate.value.toLocaleDateString(
    'en-US',
    {
      month: 'long',
      year: 'numeric'
    }
  )
})

const days = computed(() => {

  const year =
    currentDate.value.getFullYear()

  const month =
    currentDate.value.getMonth()

  const firstDay =
    new Date(year, month, 1).getDay()

  const totalDays =
    new Date(year, month + 1, 0).getDate()

  const result = []

  for (let i = 0; i < firstDay; i++) {
    result.push(null)
  }

  for (let i = 1; i <= totalDays; i++) {
    result.push(i)
  }

  return result
})

const upcoming = computed(() => {

  return applications
    .filter(app =>
      app.status === 'Interview' ||
      app.interviewDate
    )
    .sort(
      (a, b) =>
        new Date(
          a.interviewDate || a.dateApplied
        ) -
        new Date(
          b.interviewDate || b.dateApplied
        )
    )
})

function previousMonth() {
  currentDate.value = new Date(
    currentDate.value.getFullYear(),
    currentDate.value.getMonth() - 1,
    1
  )
}

function nextMonth() {
  currentDate.value = new Date(
    currentDate.value.getFullYear(),
    currentDate.value.getMonth() + 1,
    1
  )
}
</script>

<template>
  <div class="page">

    <header class="simple-header">

      <h1>Calendar</h1>

    </header>

    <div class="page-content">

      <section class="calendar-card">

        <div class="calendar-heading">

          <button @click="previousMonth">
            ‹
          </button>

          <strong>
            {{ monthName }}
          </strong>

          <button @click="nextMonth">
            ›
          </button>

        </div>

        <div class="weekdays">

          <span>Sun</span>
          <span>Mon</span>
          <span>Tue</span>
          <span>Wed</span>
          <span>Thu</span>
          <span>Fri</span>
          <span>Sat</span>

        </div>

        <div class="calendar-grid">

          <div
            v-for="(day, index) in days"
            :key="index"
            class="calendar-day"
            :class="{
              today:
                day === new Date().getDate() &&
                currentDate.getMonth() === new Date().getMonth() &&
                currentDate.getFullYear() === new Date().getFullYear()
            }"
          >
            {{ day }}
          </div>

        </div>

        <div class="calendar-legend">

          <span>
            <i class="green-dot"></i>
            Application Date
          </span>

          <span>
            <i class="purple-dot"></i>
            Interview Date
          </span>

          <span>
            <i class="orange-dot"></i>
            Other Date
          </span>

        </div>

      </section>

      <div class="section-heading">
        <h2>Upcoming Interviews</h2>

        <RouterLink to="/applications">
          View All
        </RouterLink>
      </div>

      <div
        v-if="upcoming.length"
        class="application-list"
      >

        <RouterLink
          v-for="app in upcoming"
          :key="app.id"
          :to="`/applications/${app.id}`"
          class="interview-card"
        >

          <div class="company-logo">
            {{ app.company.charAt(0) }}
          </div>

          <div>
            <strong>{{ app.company }}</strong>
            <span>{{ app.position }}</span>

            <small>
              {{ app.interviewDate || app.dateApplied }}
              ·
              {{ app.interviewTime || 'Time TBD' }}
              ·
              {{ app.interviewType || 'Online' }}
            </small>
          </div>

        </RouterLink>

      </div>

      <div
        v-else
        class="small-empty"
      >
        <span>📅</span>
        <p>No upcoming interviews.</p>
      </div>

    </div>

  </div>
</template>