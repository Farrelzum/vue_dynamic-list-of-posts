<script>
import { deleteComment, getComments } from "@/api/comments";
import Comment from "./Comment.vue";
import Loader from "./Loader.vue";
import Notification from "./Notification.vue";
import AddComment from "./AddComment.vue";

export default {
  name: "CommentList",
  props: {
    postId: {
      type: Number,
      required: true,
    },
  },
  components: {
    Comment,
    Loader,
    Notification,
    AddComment,
  },
  data() {
    return {
      comments: [],
      isLoading: false,
      wantAddComment: false,
      errorMessage: "",
    };
  },
  methods: {
    closeForm() {
      this.wantAddComment = false;
    },
    loadComments() {
      this.errorMessage = "";
      this.isLoading = true;
      this.comments = [];

      getComments(this.postId)
        .then((data) => {
          this.comments = data;
        })
        .catch(() => {
          this.errorMessage = "Unable to load comments";
        })
        .finally(() => {
          this.isLoading = false;
        });
    },
    addComment(newComment) {
      this.comments.push(newComment);
      this.closeForm();
    },
    deleteComment(commentId) {
      this.errorMessage = "";

      deleteComment(commentId)
        .then(() => {
          this.comments = this.comments.filter(
            (comment) => comment.id !== commentId,
          );
          this.closeForm();
        })
        .catch(() => (this.errorMessage = "Unable to delete comment"));
    },
  },
  watch: {
    postId() {
      this.loadComments();
    },
  },
  mounted() {
    this.loadComments();
  },
};
</script>

<template>
  <div class="block">
    <Loader v-if="isLoading" />
    <Notification
      v-if="errorMessage"
      :errorMessage="errorMessage"
      @close="errorMessage = ''"
    />
    <template v-if="!isLoading">
      <div v-if="comments.length === 0 && !errorMessage" class="block">
        <p class="title is-4">No comments yet</p>
      </div>
      <AddComment
        v-if="wantAddComment"
        :postId="postId"
        @cancel="closeForm"
        @success="addComment"
      />
      <Comment
        v-else
        v-for="comment of comments"
        :key="comment.id"
        :comment="comment"
        @delete="deleteComment"
      />
    </template>
    <button
      type="button"
      class="button is-link"
      @click="this.wantAddComment = true"
    >
      Write a comment
    </button>
  </div>
</template>
