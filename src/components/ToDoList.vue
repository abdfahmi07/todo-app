<template>
  <div class="container">
    <div class="add-item" @click="addItem">
      <div class="add-icon">
        <i class="fas fa-plus"></i>
      </div>
      <form @submit.prevent="addItem">
        <input type="text" placeholder="Add a to-do...." v-model="entry" />
      </form>
      <div class="feature-icon" :class="{ 'red-bg': entryFavorite }">
        <i
          v-if="!entryFavorite"
          class="far fa-star"
          @click="entryFavorite = !entryFavorite"
        ></i>
        <i
          v-else
          class="fas fa-star"
          @click="entryFavorite = !entryFavorite"
        ></i>
      </div>
    </div>
    <div class="todo-list">
      <div
        class="item"
        :class="{ show: item.show }"
        v-for="(item, index) in toDoList"
        :key="index"
      >
        <div class="item-checkbox">
          <i
            v-if="!item.complete"
            class="fas fa-square"
            @click="completeItem(item)"
          ></i>
          <i v-else class="fas fa-check-square"></i>
        </div>
        <div class="item-title">
          {{ item.title }}
        </div>
        <div class="feature-icon" :class="{ 'red-bg': item.favorite }">
          <i
            v-if="!item.favorite"
            class="far fa-star"
            @click="setFavoriteItem(item)"
          ></i>
          <i
            v-else
            class="fas fa-star"
            @click="item.favorite = !item.favorite"
          ></i>
        </div>
      </div>
    </div>
    <div class="show-completed" v-if="completedToDoList.length > 0">
      <div class="button" @click="showCompletedList = !showCompletedList">
        <span v-if="!showCompletedList">show</span>
        <span v-else>hide</span> completed to-do's
      </div>
    </div>
    <div class="todo-list complete-list" v-if="showCompletedList">
      <div
        class="item"
        :class="{ show: item.show }"
        v-for="(item, index) in completedToDoList"
        :key="index"
      >
        <div class="item-checkbox">
          <i v-if="!item.complete" class="fas fa-square"></i>
          <i
            v-else
            class="fas fa-check-square"
            @click="uncompleteItem(item)"
          ></i>
        </div>
        <div class="item-title">
          {{ item.title }}
        </div>
        <div class="feature-icon" :class="{ 'red-bg': item.favorite }">
          <i v-if="!item.favorite" class="far fa-star"></i>
          <i v-else class="fas fa-star"></i>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ToDoList",
  data() {
    return {
      entry: "",
      entryFavorite: false,
      toDoList: [],
      completedToDoList: [],
      showCompletedList: false,
    };
  },
  methods: {
    addItem() {
      if (this.entry !== "") {
        let date = new Date();
        let newEntry = {
          id: date.getTime(),
          title: this.entry,
          favorite: this.entryFavorite,
          complete: false,
          show: false,
        };

        if (newEntry.favorite) {
          // push new item on first position
          this.toDoList.splice(0, 0, newEntry);
        } else {
          // push new item on last position
          this.toDoList.push(newEntry);
        }
        setTimeout(() => {
          this.toDoList.find(
            (element) => element.id === newEntry.id
          ).show = true;
        }, 10);
      }
      this.entry = "";
      this.entryFavorite = false;
    },
    setFavoriteItem(item) {
      item.favorite = !item.favorite;
      item.show = false;

      setTimeout(() => {
        let index = this.toDoList.findIndex(
          (element) => element.id === item.id
        );
        this.toDoList.splice(index, 1);
        this.toDoList.splice(0, 0, item);
        item.show = true;
      }, 500);
    },
    completeItem(item) {
      item.complete = !item.complete;
      item.show = false;

      setTimeout(() => {
        this.completedToDoList.push(item);
        let index = this.toDoList.findIndex(
          (element) => element.id === item.id
        );
        this.toDoList.splice(index, 1);
        item.show = true;
      }, 500);
    },
    uncompleteItem(item) {
      item.complete = !item.complete;
      item.show = false;

      setTimeout(() => {
        if (item.favorite) {
          this.toDoList.splice(0, 0, item);
        } else {
          this.toDoList.push(item);
        }

        let index = this.completedToDoList.findIndex(
          (element) => element.id === item.id
        );
        this.completedToDoList.splice(index, 1);
        item.show = true;
      }, 500);
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  padding: 10px;
  width: calc(100% - 20px);
  min-height: calc(100% - 20px);

  @mixin feature-icon($color) {
    grid-column: 3/3;
    display: flex;
    justify-content: center;
    align-items: center;
    color: $color;
    font-size: 1.6rem;
    margin: 0 15px;
    cursor: pointer;
  }
  .add-item {
    display: grid;
    grid-template-columns: 70px auto 70px;
    grid-template-rows: 60px;
    width: 100%;
    height: 60px;
    background-color: rgba($color: #000000, $alpha: 0.3);
    border-radius: 5px;
    overflow: hidden;

    .add-icon {
      grid-column: 1/1;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #fff;
      font-size: 2rem;
      cursor: pointer;
    }

    input {
      grid-column: 2/2;
      background: none;
      border: none;
      width: 100%;
      height: 60px;
      color: #fff;
      font-size: 1.6rem;

      &::placeholder {
        color: #fff;
      }

      &:focus {
        outline: none;
      }
    }
    .feature-icon {
      @include feature-icon(#fff);
    }
  }
  .todo-list {
    padding-top: 20px;

    .item {
      display: grid;
      grid-template-columns: 70px auto 70px;
      grid-template-rows: 60px;
      width: 100%;
      height: 60px;
      margin-bottom: 2px;
      background-color: rgba($color: #fff, $alpha: 0.8);
      border-radius: 5px;
      overflow: hidden;
      opacity: 0;
      transition: all 0.5s linear;

      .item-checkbox {
        grid-column: 1/1;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 1.6rem;
        color: #99c191;
        cursor: pointer;
      }
      .item-title {
        grid-column: 2/2;
        display: flex;
        justify-content: flex-start;
        align-items: center;
        font-size: 1.6rem;
      }
      .feature-icon {
        @include feature-icon(#333);
      }
    }
    .show {
      opacity: 1;
    }
  }

  .complete-list {
    .item {
      position: relative;
      background-color: rgba($color: #fff, $alpha: 0.4);

      &::before {
        content: "";
        position: absolute;
        top: 30px;
        width: calc(100% - 120px);
        margin: 0 60px;
        border-bottom: 2px solid #555;
      }
    }
  }
  .show-completed {
    display: flex;
    justify-content: center;
    align-items: center;
    padding-top: 20px;

    .button {
      color: #fff;
      padding: 10px;
      font-size: 1.3rem;
      text-transform: uppercase;
      background-color: rgba($color: #000000, $alpha: 0.3);
      border-radius: 5px;
      overflow: hidden;
      cursor: pointer;
    }
  }
  .red-bg {
    background-color: #c23741;
    color: #fff !important;
  }
}
</style>
