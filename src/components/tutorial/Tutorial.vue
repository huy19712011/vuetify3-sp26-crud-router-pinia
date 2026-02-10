<template>
  <v-container>
    <div v-if="currentTutorial"
         class="edit-form py-3"
    >
      <p class="headline">Edit Tutorial</p>

      <v-form ref="form"
              lazy-validation
      >
        <v-text-field
          v-model="currentTutorial.title"
          :rules="[(v) => !!v || 'Title is required']"
          label="Title"
          required
        ></v-text-field>

        <v-text-field
          v-model="currentTutorial.description"
          :rules="[(v) => !!v || 'Description is required']"
          label="Description"
          required
        ></v-text-field>

        <label><strong>Status:</strong></label>
        {{ currentTutorial.published ? "Published" : "Pending" }}

        <v-divider class="my-5"></v-divider>

        <v-btn v-if="currentTutorial.published"
               class="mr-2"
               color="primary"
               small
               @click="updatePublished(false)"
        >
          UnPublish
        </v-btn>

        <v-btn v-else
               class="mr-2"
               color="primary"
               small
               @click="updatePublished(true)"
        >
          Publish
        </v-btn>

        <v-btn class="mr-2"
               color="error"
               small
               @click="deleteTutorial"
        >
          Delete
        </v-btn>

        <v-btn color="success"
               small
               @click="updateTutorial"
        >
          Update
        </v-btn>
      </v-form>

      <p class="mt-3">{{ message }}</p>
    </div>

    <div v-else>
      <p>Please click on a Tutorial...</p>
    </div>
  </v-container>
</template>

<script setup>
import {onMounted, ref} from "vue";
import {useRoute, useRouter} from "vue-router";
import TutorialDataService from "@/services/TutorialDataService";

// --- State ---
const route = useRoute();
const router = useRouter();

const currentTutorial = ref(null);
const message = ref("");

// --- Methods ---

const getTutorial = (id) => {
  TutorialDataService.get(id)
    .then((response) => {
      currentTutorial.value = response.data;
      console.log(response.data);
    })
    .catch((e) => {
      console.log(e);
    });
};

const updatePublished = (status) => {
  const data = {
    id: currentTutorial.value.id,
    title: currentTutorial.value.title,
    description: currentTutorial.value.description,
    published: status,
  };

  TutorialDataService.update(currentTutorial.value.id, data)
    .then((response) => {
      currentTutorial.value.published = status;
      console.log(response.data);
    })
    .catch((e) => {
      console.log(e);
    });
};

const updateTutorial = () => {
  TutorialDataService.update(currentTutorial.value.id, currentTutorial.value)
    .then((response) => {
      console.log(response.data);
      message.value = "The tutorial was updated successfully!";
    })
    .catch((e) => {
      console.log(e);
    });
};

const deleteTutorial = () => {
  TutorialDataService.delete(currentTutorial.value.id)
    .then((response) => {
      console.log(response.data);
      router.push({name: "tutorials"});
    })
    .catch((e) => {
      console.log(e);
    });
};

// --- Lifecycle ---
onMounted(() => {
  message.value = "";
  getTutorial(route.params.id);
});
</script>

<style>
.edit-form {
  max-width: 400px;
  margin: auto;
}
</style>
