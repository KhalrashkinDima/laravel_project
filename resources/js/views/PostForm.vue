<template>
  <div>
    <div class="container-fluid align-items-center">
      <div class="pt-2 pb-2">
        <h4 class="text-center">
          {{ ThisId.title }}
        </h4>
        <span>
          <img :src="ThisId.url" class="d-block img-fluid" />
        </span>
        <div>
          {{ ThisId.newsText }}
        </div>
        Оценить новость
        <button @click="Dec" class="rate_button">
          <img src="/img/dislike.png" class="rate_image" />
        </button>
        {{ ThisId.count }}
        <button @click="Inc" class="rate_button">
          <img src="/img/like.png" class="rate_image" />
        </button>
      </div>
      <div class="text-center p-3">
        <button
          class="btn btn-secondary"
          @click="OpenModal"
          :id="ThisId.id"
          v-if="AdminTrue"
        >
          Редактировать пост
        </button>
      </div>
      <modal-redact
        v-if="moduleRedactShown"
        @close="CloseRedact"
        v-bind:postRedact="ThisId.id"
      />
      <div class="h5 text-center">Комментарии</div>
      <div class="m-2" v-for="comment in CommentsList">
        <div class="comments">
          <div class="comment d-flex align-items-center">
            <div
              class="col-4 text-center align-items-center m-2 comment_author"
            >
              <div>Автор комментария</div>
              <div>{{ comment.authorName }}</div>
            </div>
            <div class="col-8 comment-text text-center align-items-center">
              <div class="p-1 m-4">{{ comment.commentText }}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="comment_form p-2 text-center mb-2 card bg-light align-items-center">
        <my-input label="Желаете оставить комментарий?" v-model="commentText" />
        <button class="btn btn-secondary mt-4 col-6" @click="CommentSend">
          Оставить комментарий
        </button>
      </div>
    </div>
  </div>
</template>
<script>
import { mapGetters, mapState } from "vuex";
import ModalRedact from "../components/ModalRedact.vue";
export default {
  components: { ModalRedact },
  data() {
    return {
      moduleRedactShown: false,
      show: false,
      commentText: "",
      authorName: "",
      commentDate: "",
      postId: "",
      id: "",
    };
  },
  computed: {
    ...mapGetters({
      GetPostById: "posts/GetPostById",
      ReturnName: "users/ReturnName",
      GetPostComments: "comments/GetPostComments",
    }),
    ThisId() {
      return this.GetPostById(this.$route.params.id);
    },
    AdminTrue() {
      return this.$store.getters["users/isAdmin"];
    },
    AuthTrue() {
      return this.$store.getters["users/isAuth"];
    },
    WhatYourName() {
      return this.$store.getters["users/ReturnName"];
    },
    CommentsList() {
      return this.GetPostComments(this.$route.params.id);
    },
    MyUid() {
      const User = this.$store.getters["users/ReturnUid"];
      return User;
    },
  },

  methods: {
    Inc() {
      const wasLiked = Object.keys(this.ThisId.likes).filter(
        (element) => element == this.MyUid
      );
      if (wasLiked.length > 0) {
        alert("Вы уже оценивали новость");
        return null;
      } else if (this.AuthTrue !== true) {
        alert("Зарегистрируйтесь чтобы иметь возможность оценивать новости!");
        this.$router.push("/RegistrationForm");
        return null;
      }
      this.$store.dispatch("posts/IncreaseCount", this.$route.params.id);
      this.$store.dispatch("posts/CreateLike", {
        likeId: this.ThisId.id,
        uid: this.$store.getters["users/ReturnUid"],
      });
    },
    Dec() {
      const wasLiked = Object.keys(this.ThisId.likes).filter(
        (element) => element == this.MyUid
      );
      if (wasLiked.length > 0) {
        alert("Вы уже оценивали новость");
        return null;
      } else if (this.AuthTrue !== true) {
        alert("Зарегистрируйтесь чтобы иметь возможность оценивать новости!");
        this.$router.push("/RegistrationForm");
        return null;
      }
      this.$store.dispatch("posts/DecreaseCount", this.$route.params.id);
      this.$store.dispatch("posts/CreateLike", {
        likeId: this.ThisId.id,
        uid: this.$store.getters["users/ReturnUid"],
      });
    },
    CloseRedact() {
      this.moduleRedactShown = false;
    },
    OpenModal() {
      this.moduleRedactShown = true;
    },
    CommentSend() {
      if (this.AuthTrue !== true) {
        alert("Чтобы оставить комментарий зарегистрируйтесь");
        this.$router.push("/RegistrationForm");
        return null;
      }
      this.$store.dispatch("comments/CreateComment", {
        authorName: this.$store.getters["users/ReturnName"],
        commentText: this.commentText,
        date: new Date().toString(),
        postId: this.ThisId.id,
        id: this.$store.getters["comments/GetCommentId"],
      });
    },
  },
};
</script>
<style>
.comments {
  border: 1px solid #3f5062;
  border-radius: 1rem;
}
.user_icon {
  max-height: 50px;
}
/* .comment_form {
  background: linear-gradient(#747677, #3f5062);
  border-radius: 10px;
} */
.comment_author {
  border: 1px solid #3f5062;
  border-radius: 1rem;
}
</style>

