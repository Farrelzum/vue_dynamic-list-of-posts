<script>
import { postComment } from "@/api/comments";
import InputField from "./InputField.vue";
import TextAreaField from "./TextAreaField.vue";

export default {
  name: "AddComment",
  props: {
    postId: {
      type: Number,
      required: true,
    },
  },
  components: {
    InputField,
    TextAreaField,
  },
  data() {
    return {
      name: "",
      email: "",
      body: "",
      errors: {
        name: null,
        email: null,
        body: null,
      },
      isSubmitting: false,
    };
  },
  methods: {
    handleSubmit() {
      this.errors = { name: "", email: "", body: "" };

      if (!this.name) {
        this.errors.name = "Name is required";
      }
      if (!this.email) {
        this.errors.email = "Email is required";
      }
      if (!this.body) {
        this.errors.body = "Body is required";
      }

      if (this.errors.name || this.errors.email || this.errors.body) return;

      this.isSubmitting = true;

      postComment({
        postId: this.postId,
        name: this.name,
        email: this.email,
        body: this.body,
      })
        .then((newComment) => this.$emit("success", newComment))
        .catch(() => alert("Failed to add comment"))
        .finally(() => (this.isSubmitting = false));
    },
  },
};
</script>

<template>
  <div class="content">
    <form @submit.prevent="handleSubmit">
      <InputField
        v-model="name"
        name="name"
        label="Author name"
        placeholder="Name Surname"
        :error="errors.name"
      />
      <InputField
        v-model="email"
        name="email"
        label="Author email"
        placeholder="Your email"
        icon="envelope"
        :error="errors.email"
      />
      <TextAreaField
        v-model="body"
        name="Body"
        label="Write Post Body"
        placeholder="Comment"
        :error="errors.body"
      />

      <div class="field is-grouped">
        <div class="control">
          <button
            type="submit"
            class="button is-link"
            :class="{ 'is-loading': isSubmitting }"
          >
            Add Comment
          </button>
        </div>
        <div class="control">
          <button
            type="button"
            class="button is-link is-light"
            @click="$emit('cancel')"
          >
            Cancel
          </button>
        </div>
      </div>
    </form>
  </div>
</template>
