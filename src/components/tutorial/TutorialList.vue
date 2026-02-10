<template>
  <v-row align="center" class="list px-3 mx-auto">
    <v-col cols="12" md="8">
      <v-text-field v-model="title" label="Search by Title"></v-text-field>
    </v-col>

    <v-col cols="12" md="4">
      <v-btn small @click="searchTitle"> Search</v-btn>
    </v-col>

    <v-col cols="12" sm="12">
      <v-card class="mx-auto" tile>
        <v-card-title>Tutorials</v-card-title>

        <v-data-table
          :headers="headers"
          :hide-default-footer="true"
          :items="tutorials"
          disable-pagination
        >
          <template v-slot:[`item.actions`]="{ item }">
            <v-icon class="mr-2" small @click="editTutorial(item.id)">
              mdi-pencil
            </v-icon>
            <v-icon small @click="deleteTutorial(item.id)">
              mdi-delete
            </v-icon>
          </template>
        </v-data-table>

        <v-card-actions v-if="tutorials.length > 0">
          <v-btn color="error" small @click="removeAllTutorials">
            Remove All
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
  </v-row>
</template>

<script setup>
import {onMounted, ref} from "vue";
import {useRouter} from "vue-router";
import TutorialDataService from "@/services/TutorialDataService";

// --- State ---
const tutorials = ref([]);
const title = ref("");
const router = useRouter();

const headers = [
  {title: "Title", align: "start", sortable: true, key: "title"},
  {title: "Description", key: "description", sortable: true},
  {title: "Status", key: "status", sortable: true},
  {title: "Actions", key: "actions", sortable: true},
];

// --- Methods ---

const getDisplayTutorial = (tutorial) => {
  return {
    id: tutorial.id,
    title:
      tutorial.title.length > 30
        ? tutorial.title.slice(0, 30) + "..."
        : tutorial.title,
    description:
      tutorial.description.length > 30
        ? tutorial.description.slice(0, 30) + "..."
        : tutorial.description,
    status: tutorial.published ? "Published" : "Pending",
  };
};

const retrieveTutorials = () => {
  TutorialDataService.getAll()
    .then((response) => {
      tutorials.value = response.data.map(getDisplayTutorial);
      console.log(response.data);
    })
    .catch((e) => {
      console.log(e);
    });
};

const refreshList = () => {
  retrieveTutorials();
};

const removeAllTutorials = () => {
  TutorialDataService.deleteAll()
    .then((response) => {
      console.log(response.data);
      refreshList();
    })
    .catch((e) => {
      console.log(e);
    });
};

const searchTitle = () => {
  TutorialDataService.findByTitle(title.value)
    .then((response) => {
      tutorials.value = response.data.map(getDisplayTutorial);
      console.log(response.data);
    })
    .catch((e) => {
      console.log(e);
    });
};

const editTutorial = (id) => {
  router.push({name: "tutorial-details", params: {id: id}});
};

const deleteTutorial = (id) => {
  TutorialDataService.delete(id)
    .then(() => {
      refreshList();
    })
    .catch((e) => {
      console.log(e);
    });
};

// --- Lifecycle Hooks ---
onMounted(() => {
  retrieveTutorials();
});
</script>

<style>
.list {
  max-width: 750px;
}
</style>
