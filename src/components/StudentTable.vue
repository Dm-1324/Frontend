<template>
  <v-card class="elevation-4 my-6">
    <v-card-title class="text-h5 font-weight-bold py-4" style="color: #3f51b5">
      Student List
    </v-card-title>

    <template v-if="students?.length > 0">
      <v-data-table :headers="headers" :items="students" item-key="id" class="elevation-1 pa-1">
        <template v-slot:[`item.marks`]="{ item }">
          <v-chip :color="item.marks >= 90 ? 'green' : 'blue'" small>
            {{ item.marks.toFixed(2) }}
          </v-chip>
        </template>

        <template v-slot:[`item.status`]="{ item }">
          <v-chip
            :color="
              item.status === 'ACTIVE' ? 'blue' : item.status === 'GRADUATED' ? 'green' : 'red'
            "
            small
            :variant="'tonal'"
          >
            {{ item.status }}
          </v-chip>
        </template>

        <template v-slot:[`item.actions`]="{ item }">
          <v-btn icon size="small" color="blue" variant="text" @click="$emit('edit-student', item)">
            <v-icon>mdi-pencil</v-icon>
          </v-btn>

          <v-btn
            icon
            size="small"
            color="teal"
            variant="text"
            @click="$emit('view-detail', item.id)"
          >
            <v-icon>mdi-eye</v-icon>
          </v-btn>

          <v-btn
            icon
            size="small"
            color="red"
            variant="text"
            @click="$emit('delete-student', item.id)"
          >
            <v-icon>mdi-delete</v-icon>
          </v-btn>
        </template>
      </v-data-table>
    </template>

    <template v-else>
      <v-card-text class="text-center py-10 text-grey-darken-1">
        <v-icon size="40" color="grey">mdi-account-off-outline</v-icon>
        <div class="mt-2 text-subtitle-1 font-weight-medium">No student data available</div>
      </v-card-text>
    </template>
  </v-card>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'

defineProps({
  students: {
    type: Array,
    required: true,
    default: () => [],
  },
})

defineEmits(['edit-student', 'delete-student', 'view-detail'])

const headers = [
  { title: 'ID', key: 'id' },
  { title: 'Name', key: 'name' },
  { title: 'Roll Number', key: 'rollNumber' },
  { title: 'Course', key: 'course' },
  { title: 'Marks', key: 'marks' },
  { title: 'Status', key: 'status' },
  { title: 'Actions', key: 'actions', sortable: false },
]
</script>
