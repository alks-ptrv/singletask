<template>
  <!-- Загрузка -->
  <v-card :loading="loading" class="mx-auto my-12" max-width="450">
    <template slot="progress">
      <v-progress-linear
        color="teal darken-1"
        height="3"
        indeterminate
        absolute
      ></v-progress-linear>
    </template>

    <!-- Картинка -->
    <v-img lazy-src height="175" :src="picture">
      <v-card-actions class="justify-end">
        <v-btn text color="white" @click="updateImg">
          change picture
        </v-btn>
      </v-card-actions></v-img
    >

    <!-- Поле для создания задачи -->
    <v-text-field
      v-model="newTaskTitle"
      @click:append-outer="addTask"
      @keyup.enter="addTask"
      label="New task"
      append-outer-icon="mdi-plus-circle"
      class="ma-3"
      color="teal darken-1"
    ></v-text-field>

    <!-- Список задач -->
    <v-list class="pa-0" two-line flat>
      <div v-for="task in sortedTasks" :key="task.id">
        <v-list-item-group>
          <v-list-item
            @click="completedTask(task.id)"
            :class="{ 'teal lighten-4': task.completed }"
          >
            <template v-slot:default>
              <v-list-item-action>
                <v-checkbox
                  :input-value="task.completed"
                  color="teal darken-4"
                ></v-checkbox>
              </v-list-item-action>

              <v-list-item-content>
                <v-list-item-title
                  :class="{ 'text-decoration-line-through': task.completed }"
                  >{{ task.title }}</v-list-item-title
                >
              </v-list-item-content>

              <!-- Кнопка мусорка (удаления таски) -->
              <v-list-item-action>
                <v-btn @click="deleteTask(task.id)" icon>
                  <v-icon color="teal darken-3">mdi-delete</v-icon>
                </v-btn>
              </v-list-item-action>
            </template>
          </v-list-item>

          <v-divider></v-divider>
        </v-list-item-group>
      </div>
    </v-list>
    <v-divider class="mx-4"></v-divider>

    <!-- Кнопка получения новых задача из вне -->
    <v-row align="center" justify="space-around">
      <v-btn
        text
        class="my-5"
        :loading="loading4"
        :disabled="loading4"
        color="teal darken-4"
        @click="getTasks"
      >
        GET MORE TASKS
        <template v-slot:loader>
          <span class="custom-loader">
            <v-icon light>mdi-cached</v-icon>
          </span>
        </template>
      </v-btn>
    </v-row>
  </v-card>
</template>

<script>
export default {
  name: "mainCard",

  data: () => ({
    newTaskTitle: "",
    picture: "https://picsum.photos/1920/1080?random",
    loader: null,
    loading: false,
    loading4: false,
    tasks: [
      {
        id: Date.now(),
        title: "Test Task",
        completed: false,
      },
      {
        id: Date.now() + 1,
        title: "Show who is the boss of the gym",
        completed: false,
      },
    ],
  }),

  methods: {
    async getTasks() {
      this.loader = "loading4";

      const res = await fetch(
        `https://jsonplaceholder.typicode.com/todos?_limit=3`
      );
      const newTasks = await res.json();
      // Для того что бы не было дупликтов id
      for (let index in newTasks) {
        newTasks[index].id += Math.floor(Math.random() * 1000);
        console.log(newTasks[index].id);
      }
      this.tasks = this.tasks.concat(newTasks);
    },

    updateImg() {
      this.loading = true;
      setTimeout(() => (this.loading = false), 1000);
      this.picture = `https://picsum.photos/1920/1080?random?v=${Math.floor(
        Math.random() * 10
      )}`;
    },
    addTask() {
      let newTask = {
        id: Date.now(),
        title: this.newTaskTitle,
        completed: false,
      };
      this.tasks.push(newTask);
      this.newTaskTitle = "";
    },
    completedTask(id) {
      let task = this.tasks.filter((task) => task.id === id)[0];
      task.completed = !task.completed;
    },
    deleteTask(id) {
      this.tasks = this.tasks.filter((task) => task.id !== id);
    },
  },

  computed: {
    sortedTasks() {
      return this.tasks.filter((task) => task.title);
    },
    tasksCount() {
      return sortedTasks.length;
    },
  },

  // Для крутящийся хуйни в кнопке "GET MORE TASKS"
  watch: {
    loader() {
      const l = this.loader;
      this[l] = !this[l];

      setTimeout(() => (this[l] = false), 500);

      this.loader = null;
    },
  },
};
</script>

<style>
.custom-loader {
  animation: loader 1s infinite;
  display: flex;
}
@-moz-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-webkit-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@-o-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
@keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
