<template>
  <div class="form-container">
    <h1 class="text-secondary">Ангилал нэмэх</h1>
    <form @submit.prevent="submitPost">
      <div>
        <label for="name">Нэр:</label>
        <input type="text" id="name" v-model="postData.name" required />
      </div>
      <div>
        <label for="slug">Товч нэр:</label>
        <input type="text" id="slug" v-model="postData.slug" required />
      </div>
      <label for="thumbnail">Зураг:</label>
      <input type="file" id="thumbnail" @change="handleFileUpload('thumbnail', $event)" />
      <button type="submit">Нэмэх</button>
    </form>
  </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const postData = ref({
  name: "",
  slug: "",
  thumbnail: "",
  thumbnail_name: "",
});

const handleFileUpload = (key, event) => {
  if (event.target.files && event.target.files.length > 0) {
    // Get the uploaded file
    const file = event.target.files[0];
    // Create a FileReader
    const reader = new FileReader();
    reader.onload = () => {
      postData[key] = reader.result;
      postData.thumbnail_name = file.name;
    };
    // Read the file as a data URL (base64 string)
    reader.readAsDataURL(file);
  } else {
    console.log("No file selected.");
  }
};

const submitPost = async () => {
  try {
    const { id, ...postDataWithoutId } = postData.value; // Exclude the 'id' from postData
    const postDataToSend = { ...postDataWithoutId, thumbnail: postData.thumbnail, thumbnail_name: postData.thumbnail_name };

    const csrfToken = getCookie("csrftoken");
    const response = await axios.post(
      "/api/v1/products/create_category",
      postDataToSend,
      {
        headers: {
          "Content-Type": "application/json",
          "X-CSRFToken": csrfToken,
        },
      }
    );
    console.log("Response status:", response.status);
    if (response.status === 201) {
      // Assuming 201 means successful creation
      console.log("Post created successfully:", response.data);
      postData.value.name = "";
      postData.value.slug = "";
      postData.value.thumbnail =
        "/uploads/uploads/gettyimages-999362376-612x612.jpg";
      window.alert("ангилал амжилттай үүслээ");
    }
    else {
      console.error("Failed to create post:", response.status, response.data);
    }
  } catch (error) {
    console.error("Error creating post:", error);
  }
};

// Utility to fetch CSRF token
function getCookie(name) {
  let cookieValue = null;
  if (document.cookie && document.cookie !== "") {
    const cookies = document.cookie.split(";");
    for (let i = 0; i < cookies.length; i++) {
      const cookie = cookies[i].trim();
      if (cookie.substring(0, name.length + 1) === name + "=") {
        cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
        break;
      }
    }
  }
  return cookieValue;
}
</script>

<style scoped lang="scss">
.form-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
}

.form-container label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

h1{
  text-align: center;
  display: grid;
}

.form-container input[type="text"],
.form-container input[type="number"],
.form-container input[type="file"],
.form-container textarea {
  width: 100%;
  padding: 8px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 3px;
  box-sizing: border-box;
}

.form-container button {
  padding: 8px 12px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 3px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.form-container button:hover {
  background-color: #0056b3;
}
</style>
