<script>
export default {
  name: "App",
  data() {
    return {
      commentsData: [],
      commentText: "",
      userId: 1,
      editableIndex: null,
    };
  },
  methods: {
    getComments() {
      this.commentsData = localStorage.getItem("comments")
        ? JSON.parse(localStorage.getItem("comments")).sort(
            (a, b) => new Date(b.date) - new Date(a.date)
          )
        : [];
    },
    addComment() {
      if (this.commentText) {
        const comment = {
          name: "John Doe",
          userId: this.userId,
          text: this.commentText,
          date: new Date().toLocaleString(),
          id: Math.floor(Math.random() * 1000000),
          likes: [],
          dislikes: [],
        };
        this.commentsData.push(comment);
        localStorage.setItem("comments", JSON.stringify(this.commentsData));
        this.commentText = "";
        this.getComments();
      } else {
        alert("Please enter a comment!");
      }
    },
    deleteComment(id) {
      const comments = this.commentsData.filter((comment) => comment.id !== id);
      localStorage.setItem("comments", JSON.stringify(comments));
      this.getComments();
    },
    reactionComment(id, reactionType) {
      const comment = this.commentsData.find((comment) => comment.id === id);

      if (
        comment.likes.includes(this.userId) ||
        comment.dislikes.includes(this.userId)
      ) {
        comment.likes.includes(this.userId)
          ? comment.likes.splice(comment.likes.indexOf(this.userId), 1)
          : comment.dislikes.splice(comment.dislikes.indexOf(this.userId), 1);
      } else {
        reactionType === "like"
          ? comment.likes.push(this.userId)
          : comment.dislikes.push(this.userId);
      }

      localStorage.setItem("comments", JSON.stringify(this.commentsData));
    },
    updateComment(id) {
      const comment = this.commentsData.find((comment) => comment.id === id);
      if (comment.text) {
        comment.date = new Date().toLocaleString();
        localStorage.setItem("comments", JSON.stringify(this.commentsData));
        this.editableIndex = null;
      } else {
        alert("Please enter a comment!");
      }
    },
  },
  created() {
    this.getComments();
  },
};
</script>

<template>
  <main>
    <div class="container">
      <div class="row">
        <div class="col-12 px-4 py-4 bg-white mt-5 mb-3 rounded-2">
          <div class="row">
            <div class="col-12">
              <div class="form-group">
                <div class="col-lg-12">
                  <textarea
                    name="user-message"
                    id="user-message"
                    class="form-control border-0 shadow-none"
                    rows="4"
                    placeholder="Your text here"
                    v-model="commentText"
                  ></textarea>
                </div>
              </div>
            </div>
            <div class="col-12 d-flex align-items-end justify-content-between">
              <div class="buttons d-flex">
                <button class="btn icon-btn">
                  <img src="@/assets/ionic-ios-attach.svg" />
                </button>
                <button class="btn icon-btn">
                  <vue-feather type="smile" size="16"></vue-feather>
                </button>
              </div>
              <button class="btn submit-btn me-3" @click="addComment">
                SUBMIT
              </button>
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <Transition>
          <div
            class="col-12 py-4 gap-2 px-4 rounded-3 bg-white"
            v-if="commentsData.length < 1"
          >
            <span> No comments yet ☹️ Please add one! </span>
          </div>
        </Transition>
        <TransitionGroup>
          <div
            class="col-12 rounded-3 bg-white mb-2"
            v-for="(comment, index) in commentsData"
            :key="index"
          >
            <div class="row">
              <div class="col-12 py-4 d-flex gap-2 px-4">
                <img src="@/assets/user.png" class="user-img" />
                <div class="d-flex flex-column w-100">
                  <span class="date-text mb-1">{{ comment.date }}</span>
                  <div class="w-100">
                    <span class="text-purple me-1">{{ comment.name }}</span>
                    <Transition>
                      <span v-if="!editableIndex">{{ comment.text }}</span>
                      <div v-else :class="!editableIndex ? 'h-0' : ''">
                        <textarea
                          class="form-control border-0 shadow-none w-100 p-0"
                          rows="4"
                          placeholder="Your text here"
                          v-model="comment.text"
                        ></textarea>
                        <div class="d-flex w-100 justify-content-between">
                          <div class="buttons d-flex">
                            <button class="btn icon-btn">
                              <img src="@/assets/ionic-ios-attach.svg" />
                            </button>
                            <button class="btn icon-btn">
                              <vue-feather type="smile" size="16"></vue-feather>
                            </button>
                          </div>
                          <div class="buttons d-flex">
                            <button
                              class="btn transparent-btn"
                              @click="editableIndex = null"
                            >
                              CANCEL
                            </button>
                            <button
                              class="btn purple-btn"
                              @click="updateComment(comment.id)"
                            >
                              UPDATE
                            </button>
                          </div>
                        </div>
                      </div>
                    </Transition>
                  </div>
                </div>
              </div>
              <div
                class="col-12 py-1 border-top px-4 d-flex justify-content-between align-items-center"
              >
                <div class="buttons d-flex gap-3">
                  <button
                    class="btn icon-btn px-0 gap-2"
                    @click="reactionComment(comment.id, 'like')"
                  >
                    <vue-feather
                      type="thumbs-up"
                      size="16"
                      :stroke="
                        comment.likes.includes(userId) ? '#9B59B6' : '#c1c8ce'
                      "
                    ></vue-feather>
                    <span> {{ comment.likes.length }} </span>
                  </button>
                  <button
                    class="btn icon-btn px-0 gap-2"
                    @click="reactionComment(comment.id, 'dislikes')"
                  >
                    <vue-feather
                      type="thumbs-down"
                      size="16"
                      :stroke="
                        comment.dislikes.includes(userId)
                          ? '#9B59B6'
                          : '#c1c8ce'
                      "
                    ></vue-feather>
                    <span> {{ comment.dislikes.length }} </span>
                  </button>
                </div>

                <div class="buttons d-flex">
                  <button
                    class="btn icon-btn p-2"
                    @click="editableIndex = comment.id"
                  >
                    <vue-feather type="edit-3" size="16"></vue-feather>
                  </button>
                  <button
                    class="btn icon-btn p-2"
                    @click="deleteComment(comment.id)"
                  >
                    <vue-feather type="trash-2" size="16"></vue-feather>
                  </button>
                </div>
              </div>
            </div>
          </div>
        </TransitionGroup>
      </div>
    </div>
  </main>
</template>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap");

body {
  font-family: "Roboto", sans-serif;
  background-color: #34495e;
  font-size: 14px;
  line-height: 16px;
  color: #5d6d7e;
}
.container {
  max-width: 420px;
}

@media screen and (max-width: 450px) {
  .container {
    max-width: 90%;
  }
}

.form-control {
  resize: none !important;
}
.text-purple {
  color: #9b59b6;
  font-weight: 700;
}
.bottom-area {
  &::before {
    content: "";
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 1px;
    color: #eaecee;
  }
}
.border-top {
  border-color: #eaecee;
}
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}

#user-message {
  text-align: left;
  font-size: 14px;
  letter-spacing: 0px;
  font-size: 14px;
  color: #5d6d7e;
  &::placeholder {
    color: #c1c8ce;
  }
}
.user-img {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
}

.date-text {
  text-align: left;
  font: normal normal normal 8px/9px Roboto;
  color: #99a4ae;
  opacity: 1;
  font-size: 8px;
}
.purple-btn {
  background-color: #9b59b6;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 6px 14px;
  font-size: 10px;
  letter-spacing: 1.4px;

  border-radius: 5px;
  transition: all 0.3s ease-in-out;
  &:hover {
    background-color: #6f3885;
    color: #fff;
  }
}
.transparent-btn {
  background-color: transparent;
  color: #c1c8ce;
  border: none;
  border-radius: 5px;
  padding: 6px 14px;
  font-size: 10px;
  letter-spacing: 1.4px;

  border-radius: 5px;
  transition: all 0.3s ease-in-out;
  &:hover {
    background-color: transparent;
    color: #c1c8ce;
  }
}
.submit-btn {
  background-color: #9b59b6;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 10px 25px;
  font-size: 14px;
  box-shadow: 0px 3px 6px #8e44ad29;
  letter-spacing: 1.4px;

  border-radius: 5px;
  transition: all 0.3s ease-in-out;
  &:hover {
    background-color: #6f3885;
    color: #fff;
  }
}

.icon-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  color: #c1c8ce !important;
  border: 0 !important;

  img {
    height: 16px;
  }
}
</style>
