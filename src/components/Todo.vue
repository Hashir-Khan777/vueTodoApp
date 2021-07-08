<template>
  <div class="todo-list">
    <ul class="todo-items" id="items">
      <li class="list-item" v-for="(item, index) in todos" :key="item._id">
        <div v-if="item.update" class="update_todo">
          <span>{{ index + 1 }}) </span>
          <input
            autofocus
            type="text"
            class="todoinput"
            autocomplete="off"
            :value="item.value"
            @change="(e) => changeInput(e, index)"
          />
          <button class="list-btn" @click="() => save(index, item._id)">
            Save
          </button>
        </div>

        <div v-else>
          <span>{{ index + 1 }}) </span>
          <span class="todo_item">{{ item.value }}</span>
          <button class="list-btn" @click="() => updateOne(index, item._id)">
            Update
          </button>
          <button class="list-btn" @click="() => deleteOne(index, item._id)">
            Delete
          </button>
          <button class="list-btn" @click="() => completed(index, item._id)">
            {{ !item.completed ? "complete" : "in complete" }}
          </button>
          <i :class="item.completed ? `fas fa-check tick_icon` : null"></i>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import firebase from "firebase/app";
export default {
  name: "Todos",
  props: {
    todos: Array,
  },

  methods: {
    deleteOne(index, id) {
      const firebaseRefrence = firebase.firestore().collection("todos");
      firebaseRefrence.doc(id).delete();
      const deleteTodo = this.todos;
      deleteTodo.splice(deleteTodo.indexOf(deleteTodo[index]), 1);
    },
    updateOne(index, id) {
      const firebaseRefrence = firebase.firestore().collection("todos");
      const todos = this.todos;
      firebaseRefrence.doc(id).update({ update: true });
      this.inputValue = todos[index].value;
      todos[index].update = true;
    },
    save(index, id) {
      const firebaseRefrence = firebase.firestore().collection("todos");
      let todos = this.todos;
      firebaseRefrence.doc(id).update({ value: todos[index].value });
      firebaseRefrence.doc(id).update({ update: false });
      const old_todos = [...todos];
      old_todos[index].value = todos[index].value;
      todos = old_todos;
      todos[index].update = false;
    },
    changeInput(e, index) {
      const todos = this.todos;
      todos[index].value = e.target.value;
    },
    completed(index, id) {
      const firebaseRefrence = firebase.firestore().collection("todos");
      const todos = this.todos;
      firebaseRefrence.doc(id).update({ completed: !todos[index].completed });
      todos[index].completed = !todos[index].completed;
    },
  },
};
</script>

<style>
li.list-item {
  margin-bottom: 10px;
}

.tick_icon {
  font-size: 25px !important;
  margin-left: 10px;
}

.todo_item {
  margin-right: 10px;
}

button.list-btn:hover {
  background-color: #64b5f6;
}

div.todo-list {
  display: flex;
  justify-content: center;
  margin: 40px 0px 10px 0px;
  color: #48cae4;
}

button.list-btn {
  background: transparent;
  color: white;
  border: 1px solid #64b5f6;
  padding: 10px 15px 10px 15px;
  border-radius: 20px;
  transition: 0.2s ease;
  margin: 0 5px;
}

button.list-btn:hover {
  background-color: #64b5f6;
}
</style>
