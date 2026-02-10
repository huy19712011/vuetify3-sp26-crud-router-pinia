<template>
  <div class="submit-form mt-3 mx-auto">
    <p class="headline">Add Tutorial</p>

    <div v-if="!submitted">
      <v-form ref="form" lazy-validation>
        <v-text-field
          v-model="tutorial.title"
          :rules="[(v) => !!v || 'Title is required']"
          label="Title"
          required
        ></v-text-field>

        <v-text-field
          v-model="tutorial.description"
          :rules="[(v) => !!v || 'Description is required']"
          label="Description"
          required
        ></v-text-field>
      </v-form>

      <v-btn class="mt-3" color="primary" @click="saveTutorial">Submit</v-btn>
    </div>

    <div v-else>
      <v-card class="mx-auto">
        <v-card-title> Submitted successfully!</v-card-title>

        <v-card-subtitle>
          Click the button to add new Tutorial.
        </v-card-subtitle>

        <v-card-actions>
          <v-btn color="success" @click="newTutorial">Add</v-btn>
        </v-card-actions>
      </v-card>
    </div>
  </div>
</template>

<script setup>
import {ref} from "vue";
import TutorialDataService from "@/services/TutorialDataService";

// --- State ---
const formRef = ref(null); // Reference to the <v-form>
const submitted = ref(false);

const initialTutorialState = {
  id: null,
  title: "",
  description: "",
  published: false,
};

const tutorial = ref({...initialTutorialState});

// --- Methods ---

const saveTutorial = async () => {
  // Optional: If you want to trigger Vuetify validation before saving
  // const { valid } = await formRef.value.validate();
  // if (!valid) return;

  const data = {
    title: tutorial.value.title,
    description: tutorial.value.description,
  };

  TutorialDataService.create(data)
    .then((response) => {
      tutorial.value.id = response.data.id;
      console.log(response.data);
      submitted.value = true;
    })
    .catch((e) => {
      console.log(e);
    });
};

const newTutorial = () => {
  submitted.value = false;
  // Reset the object and clear form validation errors
  tutorial.value = {...initialTutorialState};
  if (formRef.value) {
    formRef.value.resetValidation();
  }
};
</script>

<style scoped>
.submit-form {
  max-width: 300px;
}
</style>
