<template>
  <v-dialog :model-value="!!studentId" @update:model-value="$emit('close-detail')" max-width="700">
    <v-card v-if="student">
      <v-card-title class="text-h6" style="background-color: #3f51b5; color: white">
        Student Detail
      </v-card-title>
      <v-card-text class="pt-6">
        <v-row>
          <v-col cols="12" md="6">
            <v-list dense lines="1">
              <v-list-item>
                <v-list-item-title><strong>ID:</strong> {{ student.id }}</v-list-item-title>
              </v-list-item>
              <v-list-item>
                <v-list-item-title
                  ><strong>Roll Number:</strong> {{ student.rollNumber }}</v-list-item-title
                >
              </v-list-item>
              <v-list-item>
                <v-list-item-title><strong>Course:</strong> {{ student.course }}</v-list-item-title>
              </v-list-item>
              <v-list-item>
                <v-list-item-title>
                  <strong>Marks:</strong>
                  {{ student.marks }}
                </v-list-item-title>
              </v-list-item>
              <v-list-item>
                <v-list-item-title><strong>Status:</strong> {{ student.status }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-col>

          <v-col
            cols="12"
            md="6"
            class="d-flex flex-column align-center justify-center text-center"
          >
            <v-avatar size="200" color="#b2b9e1" class="mb-4">
              <v-icon size="128" color="#3f51b5">mdi-account</v-icon>
            </v-avatar>
            <p class="font-weight-bold text-h5">{{ student.name }}</p>
          </v-col>
        </v-row>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn
          color="#3f51b5"
          class="border border-md px-4 font-weight-bold"
          @click="$emit('close-detail')"
          >Close</v-btn
        >
      </v-card-actions>
    </v-card>
    <div v-else>Loading...</div>
  </v-dialog>
</template>

<script setup>
import { ref, watch, defineProps, defineEmits } from 'vue'
import axios from 'axios'

const props = defineProps({
  studentId: [Number, null],
})

defineEmits(['close-detail'])
const student = ref(null)

const fetchDetail = async (id) => {
  if (!id) {
    student.value = null
    return
  }
  try {
    const response = await axios.get(`http://localhost:8092/students/${id}`)
    student.value = response.data
  } catch (error) {
    console.error('Failed to fetch detail:', error)
  }
}

watch(
  () => props.studentId,
  (newId) => {
    fetchDetail(newId)
  },
  { immediate: true },
)
</script>
