<template>
  <div class="todo_app">
    <div class="todo-header">
      <h1 class="todo-heading">WHAT YOU SHOULD TO DO...</h1>
    </div>
    <div class="todo-input">
      <div class="input-label">
        <input
          type="text"
          required
          @change="onChange"
          :value="inputValue"
          class="whatToDo"
          id="label"
          autocomplete="off"
        />
        <label for="label" class="todo-label"> what you should to do... </label>
      </div>
      <div class="todo-btns">
        <button class="input-btn" @click="add">
          Add Item
        </button>
        <button class="input-btn" @click="deleteAll">
          Delete All
        </button>
      </div>
    </div>
    <Todos :todos="todos" />
  </div>
</template>

<script>
import Todos from "./components/Todo.vue";
import firebase from "firebase/app";
export default {
  name: "App",
  components: {
    Todos,
  },

  data() {
    return {
      todos: [],
      inputValue: "",
    };
  },

  methods: {
    onChange(e) {
      this.inputValue = e.target.value;
    },
    add() {
      const firebaseRefrence = firebase.firestore().collection("todos");
      if (this.inputValue) {
        const old_todos = [...this.todos];
        old_todos.push({
          value: this.inputValue,
          update: false,
          completed: false,
        });
        const key = firebaseRefrence.doc().id;
        firebaseRefrence
          .doc(key)
          .set({
            _id: key,
            value: this.inputValue,
            update: false,
            completed: false,
          })
          .then(() => {
            this.todos = old_todos;
          });
        this.inputValue = "";
      }
    },
    deleteAll() {
      const firebaseRefrence = firebase.firestore().collection("todos");
      firebaseRefrence.get().then((querySnapshot) => {
        querySnapshot.forEach((doc) => {
          doc.ref.delete();
        });
      });
      this.todos = [];
    },
  },

  beforeCreate() {
    const firebaseRefrence = firebase.firestore().collection("todos");
    firebaseRefrence.get().then((querySnapshot) => {
      querySnapshot.forEach((doc) => {
        this.todos.push(doc.data());
      });
    });
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: sans-serif;
  outline: none;
  list-style-type: none;
}
button,
input[type="checkbox"] {
  cursor: pointer;
}
body {
  background-color: #1c1e24;
}
.todo_app {
  display: flex;
  align-items: center;
  flex-direction: column;
  flex: 1;
  margin-top: 70px;
}
.todo_input {
  display: flex;
}

div.todo-header {
  width: 100vw;
  display: flex;
  justify-content: center;
}

h1.todo-heading {
  color: #48cae4;
  padding: 45px 0px 45px 0px;
}

div.todo-input {
  display: flex;
  flex-direction: column;
  align-items: center;
}

input.whatToDo {
  position: relative;
  display: flex;
  flex-wrap: wrap;
  margin: 0px 0px 40px 0px;
  border: none;
  background: none;
  border-bottom: 1px solid #48cae4;
  color: #48cae4;
  width: 350px;
  font-size: 19px;
  transition: 0.4s ease-in-out;
}

.todoinput {
  position: relative;
  border: none;
  background: none;
  border-bottom: 1px solid #48cae4;
  color: #48cae4;
  transition: 0.4s ease-in-out;
}

.input-label {
  position: relative;
}

label.todo-label {
  position: absolute;
  color: #48cae4;
  top: 0;
  transition: 0.4s linear;
}

input.whatToDo:focus + label.todo-label,
input.whatToDo:valid + label.todo-label {
  top: -19px;
  font-size: 12px;
  color: #64b5f6;
}

input.whatToDo:focus,
input.whatToDo:valid {
  border-bottom: 1px solid #64b5f6;
}

div.todo-btns {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
}

button.input-btn {
  background: transparent;
  color: white;
  border: 1px solid #64b5f6;
  font-weight: 700;
  padding: 10px 15px 10px 15px;
  border-radius: 20px;
  margin: 0px 10px 10px 10px;
  transition: 0.2s ease;
}

button.input-btn:hover {
  background-color: #64b5f6;
}
</style>
