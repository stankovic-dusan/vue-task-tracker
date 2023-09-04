<template>
  <div class="container">
    <TheHeader @toggle-add-task="toggleAddTask" title="Task Tracker" :showAddTask="showAddTask" />
    <div v-show="showAddTask">
      <AddTask @add-task="addTask" />
    </div>
    <Tasks @toggle-reminder="toggleReminder" @delete-task="deleteTask" :tasks="tasks" />
  </div>
</template>

<script>
import TheHeader from "./components/TheHeader.vue";
import Tasks from "./components/Tasks.vue";
import AddTask from "./components/AddTask.vue";

import { collection, onSnapshot, addDoc, doc, deleteDoc, updateDoc } from "firebase/firestore";
import { db } from "./firebase";

export default {
  name: "App",
  components: { TheHeader, Tasks, AddTask },
  data() {
    return {
      tasks: [],
      showAddTask: true,
      tasksCollection: collection(db, "tasks"),
    };
  },

  methods: {
    addTask(text, day, reminder) {
      addDoc(this.tasksCollection, {
        text: text,
        day: day,
        reminder: reminder,
      });
    },

    deleteTask(taskid) {
      if (confirm("Are you sure?")) {
        deleteDoc(doc(this.tasksCollection, taskid));
      }
    },

    toggleReminder(taskid) {
      const index = this.tasks.findIndex((task) => task.id === taskid);

      updateDoc(doc(this.tasksCollection, taskid), {
        reminder: !this.tasks[index].reminder,
      });
    },

    toggleAddTask() {
      this.showAddTask = !this.showAddTask;
    },
  },

  mounted() {
    onSnapshot(this.tasksCollection, (querySnapshot) => {
      const fbTasks = [];
      querySnapshot.forEach((doc) => {
        // console.log(doc.id, " => ", doc.data());
        const task = {
          id: doc.id,
          text: doc.data().text,
          day: doc.data().day,
          reminder: doc.data().reminder,
        };
        fbTasks.push(task);
      });
      this.tasks = fbTasks;
    });
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap");

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: "Poppins", sans-serif;
}

.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}

.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}

.btn:focus {
  outline: none;
}

.btn:active {
  transform: scale(0.98);
}

.btn-block {
  display: block;
  width: 100%;
}
</style>
