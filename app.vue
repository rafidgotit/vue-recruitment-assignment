<template>
  <div class="wrapper">
    <h2>Submit a Complaint</h2>

    <!-- Form to add a new complaint -->
    <div class="complain-form">
      <input v-model="title" type="text" placeholder="Title" />
      <textarea v-model="body" placeholder="Enter your complaint"></textarea>

      <button @click="saveComplain">Submit Complaint</button>

      <!-- Place Simple text loader when saving -->

      <!-- Show Error message if any error is occured-->
    </div>

    <h2>Complaints List</h2>

    <!-- Simple text loader when loading -->
    <div v-if="isLoading">Loading...</div>
    <!-- List of complaints -->
    <template v-else-if="complains.length">
      <div
        v-for="complain in complains"
        :key="complain.Id"
        class="complain-item"
      >
        <h3>{{ complain.Title }}</h3>
        <p>{{ complain.Body }}</p>
      </div>
    </template>
    <div v-else>
      <p>No complaints available.</p>
    </div>
  </div>
</template>

<script setup>
const baseUrl = "https://sugarytestapi.azurewebsites.net/";
const listPath = "TestApi/GetComplains";
const savePath = "TestApi/SaveComplain";

const complains = ref([]);
const title = ref("");
const body = ref("");
const isSaving = ref(false);
const errorMessage = ref("");

const isLoading = ref(false);

// Fetch complaints from the API
const fetchComplains = async () => {
  isLoading.value = true;
  const response = await fetch(`${baseUrl}${listPath}`);
  const data = await response.json();
  complains.value = data;
  isLoading.value = false;
};

// Save a new complaint
const saveComplain = async () => {
  try {
    isSaving.value = true;
    const response = await fetch(`${savePath}`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        Title: "Test Title",
        Body: "Test Body",
      }),
    });
    const data = await response.json();
    console.log(data);
    if (!data.Success) throw new Error("Failed to save complaint.");
    // update list of conplaints on success
  } catch (e) {
    // handle error
  } finally {
    isSaving.value = false;
  }
};

onMounted(() => {
  // call fetchComplains when component is mounted
});
</script>

<style>
.wrapper {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}

.complain-form {
  margin-bottom: 20px;
}

.complain-form input,
.complain-form textarea {
  width: 100%;
  margin-bottom: 10px;
  padding: 8px;
  box-sizing: border-box;
}

.complain-form button {
  padding: 8px;
}

.error-message {
  color: red;
  margin-top: 10px;
}
</style>
