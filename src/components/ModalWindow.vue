<template>
  <div class="modal__mask" id="modalMask" @click="closeModal">
    <div class="modal__wrapper">
      <img
        class="modal__image"
        :src="this.imageData.url"
        alt="Big gallery image"
      />
      <div v-if="commentsList" class="modal__comments">
        <div class="comment__wrapper">
          <div
            class="comment"
            v-for="comment in commentsList"
            :key="comment.id"
          >
            <p class="comment__user">{{ comment.name }}</p>
            <p class="comment__date">{{ getFormattedDate(comment.date) }}</p>
            <p class="comment__text">{{ comment.text }}</p>
          </div>
        </div>
      </div>
      <div class="modal__comment-input">
        <label class="label_textarea" for="textarea">Comment</label>
        <textarea
          class="textarea"
          name="comment"
          id="textarea"
          v-model="newComment.text"
          rows="5"
        ></textarea>
        <p class="help-text">Write a few sentences about the photo.</p>
      </div>
      <div class="modal__buttons">
        <button class="btn_modal" @click="sendComment">Save</button>
        <button class="btn_modal" id="closeBtn">Close</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ModalWindow",
  props: {
    selectedImgId: String,
  },
  emits: ["closeModal"],

  data() {
    return {
      imageData: [],
      newComment: {
        id: "",
        name: "User",
        text: "",
        date: "",
      },
    };
  },

  created() {
    this.loadImageData(this.selectedImgId);
  },

  computed: {
    commentsList() {
      if (!this.imageData.comments || this.imageData.comments.length === 0) {
        return false;
      }
      return this.imageData.comments;
    },
  },

  methods: {
    loadImageData(id) {
      fetch(`https://boiling-refuge-66454.herokuapp.com/images/${id}`)
        .then((response) => response.json())
        .then((data) => {
          data.comments = data.comments.map((item) => {
            return { ...item, name: "User" };
          });
          this.imageData = data;
        });
    },

    closeModal(event) {
      const id = event.target.id;
      if (id === "modalMask" || id === "closeBtn") {
        this.$emit("closeModal");
      }
    },

    getFormattedDate(timestamp) {
      return new Date(timestamp).toLocaleString();
    },

    sendComment() {
      if (this.newComment.text === "") {
        return;
      }
      this.newComment.id = Date.now();
      this.newComment.date = Date.now();
      this.imageData.comments.push(this.newComment);
      const { name, text: comment } = this.newComment;
      fetch(
        `https://boiling-refuge-66454.herokuapp.com/images/${this.selectedImgId}/comments`,
        {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({ name, comment }),
        }
      );
      this.newComment = {
        id: "",
        name: "User",
        text: "",
        date: "",
      };
    },
  },
};
</script>

<style scoped>
.modal__mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(107, 114, 128, 0.75);
  display: flex;
  justify-content: center;
  align-items: center;
}
.modal__wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 24px;
  gap: 24px;
  max-width: 692px;
  max-height: 100vh;
  background: #ffffff;
  box-shadow: 0px 20px 25px -5px rgba(0, 0, 0, 0.1),
    0px 10px 10px -5px rgba(0, 0, 0, 0.04);
  border-radius: 8px;
  overflow: auto;
}
.modal__image {
  width: min(calc(100vw - 48px), 450px);
  box-shadow: 0px 25px 50px -12px rgba(0, 0, 0, 0.25);
  border-radius: 24px;
}
.modal__comments {
  width: 100%;
  min-height: 50px;
  overflow-y: auto;
}
.comment__wrapper {
  display: flex;
  flex-direction: column;
  row-gap: 1em;
}
.comment {
  display: flex;
  flex-wrap: wrap;
  column-gap: 1em;
  row-gap: 0.5em;
}
.comment__date {
  font-weight: 400;
  opacity: 0.5;
}
.comment__text {
  flex: 1 1 100%;
  font-weight: 400;
}
.modal__comment-input {
  display: flex;
  flex-direction: column;
  row-gap: 8px;
}
.textarea {
  resize: vertical;
  width: min(calc(100vw - 48px), 450px);
  max-height: 30vh;
  min-height: 1.6em;
  padding: 0 5px;
  background: #ffffff;
  border: 1px solid #d1d5db;
  box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 6px;
}
.help-text {
  font-weight: 400;
  color: #6b7280;
}
.modal__buttons {
  display: flex;
  column-gap: 10px;
}
.btn_modal {
  padding: 9px 17px;
  background: #4f46e5;
  box-shadow: 0px 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 6px;
  color: white;
}
@media screen and (hover: hover) {
  .btn_modal:hover {
    filter: opacity(90%);
    transition: filter 0.2s;
  }
  .btn_modal:active {
    filter: opacity(100%);
    transition: filter 0s;
  }
}
</style>
