<template>
  <v-container>
    <v-card class="mb-6 elevation-4">
      <v-card-title class="text-h5 font-weight-bold mb-4" style="color: #3f51b5">
        {{ editingStudent.id ? 'Edit Student Details' : 'Add New Student' }}
      </v-card-title>
      <v-card-text>
        <v-form @submit.prevent="submitForm">
          <v-row>
            <v-col cols="12" md="4">
              <v-text-field
                name="name"
                label="Name"
                :rules="[rules.required, rules.namePattern]"
                variant="outlined"
                v-model="editingStudent.name"
                :disabled="!!editingStudent.id"
                prepend-icon="mdi-account"
              ></v-text-field>
            </v-col>
            <v-col cols="12" md="4">
              <v-text-field
                name="rollNo"
                label="Roll Number"
                :rules="[rules.required]"
                variant="outlined"
                v-model="editingStudent.rollNumber"
                :disabled="!!editingStudent.id"
                prepend-icon="mdi-numeric"
              ></v-text-field>
            </v-col>
            <v-col cols="12" md="4">
              <v-select
                :items="courseOptions"
                label="Course"
                :rules="[rules.required]"
                variant="outlined"
                v-model="editingStudent.course"
                prepend-icon="mdi-bookshelf"
              ></v-select>
            </v-col>
            <v-col cols="12" md="4">
              <v-select
                :items="yearOptions"
                label="Year"
                :rules="[rules.required]"
                variant="outlined"
                v-model="editingStudent.year"
                :disabled="!!editingStudent.id"
                prepend-icon="mdi-chart-box-outline"
              ></v-select>
            </v-col>
            <v-col cols="12" md="4">
              <v-text-field
                name="marks"
                label="Marks"
                :rules="[rules.required, rules.digitRange, rules.marksRange]"
                variant="outlined"
                v-model="editingStudent.marks"
                prepend-icon="mdi-checkbox-marked-outline"
              ></v-text-field>
            </v-col>
            <v-col cols="12" md="4">
              <v-select
                :items="statusOptions"
                v-model="editingStudent.status"
                label="Status"
                :rules="[rules.required]"
                variant="outlined"
                prepend-icon="mdi-school"
              ></v-select>
            </v-col>
            <v-card-actions>
              <v-btn
                type="submit"
                color="success"
                class="border border-md border-success bg-green-lighten-5 px-4 font-weight-bold"
                >{{ editingStudent.id ? 'Update Student Data' : 'Submit' }}</v-btn
              >
              <v-btn
                @click="resetForm"
                color="error"
                variant="text"
                class="border border-error border-md bg-red-lighten-5 px-4 font-weight-bold"
                >{{ editingStudent.id ? 'Cancel' : 'Reset' }}</v-btn
              >
            </v-card-actions>
          </v-row>
        </v-form>
      </v-card-text>
    </v-card>

    <v-progress-linear color="#3f51b5" indeterminate :height="5" v-if="loading"></v-progress-linear>
    <student-table
      v-else
      :students="students"
      @edit-student="startEdit"
      @delete-student="deleteStudent"
      @view-detail="selectedDetailId = $event"
    ></student-table>
    <student-detail :studentId="selectedDetailId" @close-detail="selectedDetailId = null" />
  </v-container>
</template>

<script setup>
import StudentTable from './StudentTable.vue'
import StudentDetail from './StudentDetail.vue'
import { ref, onMounted } from 'vue'
import axios from 'axios'

const API_URL = 'http://localhost:8092/students'

const students = ref([])
const loading = ref(false)
const editingStudent = ref({})
const selectedDetailId = ref(null)

const courseOptions = ['CS', 'EE', 'ME', 'IT']
const yearOptions = [1, 2, 3, 4]
const statusOptions = ['ACTIVE', 'GRADUATED', 'SUSPENDED']
const rules = {
  required: (v) => !!v || 'Entry in this field is Required.',
  namePattern: (v) => /^[a-zA-Z\s]+$/.test(v) || 'Invalid Name!! Only Letters are allowed',
  digitRange: (v) =>
    /^\d+(\.\d{1,2})?$/.test(v) || 'Only Numbers are allowed and decimal upto 2 places !!',
  marksRange: (v) => (v >= 0 && v <= 100) || 'Marks cannot be more than 100 or less than 0',
}

const fetchStudents = async () => {
  loading.value = true
  try {
    const response = await axios.get(API_URL)
    students.value = response.data
    console.log(students)
  } catch (error) {
    console.error('Fetch failed:', error)
  } finally {
    loading.value = false
  }
}

const submitForm = async () => {
  loading.value = true
  const isEditing = !!editingStudent.value.id
  const url = isEditing ? `${API_URL}/${editingStudent.value.id}` : API_URL

  try {
    if (isEditing) {
      const patchPayload = {
        course: editingStudent.value.course,
        marks: editingStudent.value.marks,
        status: editingStudent.value.status,
      }
      await axios.patch(url, patchPayload)
    } else {
      await axios.post(url, editingStudent.value)
    }
    resetForm()
    await fetchStudents()
  } catch (error) {
    console.error('Submission failed:', error.response?.data)
  } finally {
    loading.value = false
  }
}

const startEdit = (student) => {
  editingStudent.value = { ...student }
  scrollToTop()
}

const deleteStudent = async (id) => {
  if (!confirm('Are you sure you want to delete this student?')) return
  try {
    await axios.delete(`${API_URL}/${id}`)
    await fetchStudents()
  } catch (error) {
    console.error('Deletion failed:', error)
  }
}

const resetForm = () => {
  editingStudent.value = {}
}

const scrollToTop = () => {
  window.scrollTo({
    top: 0,
    behavior: 'smooth',
  })
}

onMounted(fetchStudents)
</script>
