<template>
  <div class="home">
    <v-container>
      <v-row>
        <h2>Add ToDo</h2>
      </v-row>
      <v-form @submit="addTodo">
        <v-row
          align="center"
          justify="center"
        >
          <v-col cols="12" sm="4">
            <v-text-field v-model="newText" label="新規ToDo" />
          </v-col>
          <v-col cols="12" sm="1">
            <div class="add-btn">
              <v-btn class="mx-2" fab small @click="addTodo">
                <v-icon dark>mdi-plus</v-icon>
              </v-btn>
            </div>
          </v-col>
          <v-spacer />
        </v-row>
      </v-form>
      <v-row>
        <h2>Kanban</h2>
      </v-row>
      <v-row
        align="center"
        justify="center"
      >
        <v-col cols="12" sm="4">
          <status-box
                      :taskList="todo" 
                      @moveCard="moveCard" 
                      @dropCard="dropCard('todo')">
            <template v-slot:titlebar>
              <v-toolbar-title>Todo</v-toolbar-title>
            </template>
          </status-box>
        </v-col>
        <v-col cols="12" sm="4">
          <status-box
                      :taskList="doing" 
                      @moveCard="moveCard" 
                      @dropCard="dropCard('doing')">
            <template v-slot:titlebar>
              <v-toolbar-title>Doing</v-toolbar-title>
            </template>
          </status-box>
        </v-col>
        <v-col cols="12" sm="4">
          <status-box
                      :taskList="done" 
                      @moveCard="moveCard" 
                      @dropCard="dropCard('done')">
            <template v-slot:titlebar>
              <v-toolbar-title>Done</v-toolbar-title>
              <v-spacer />
              <v-tooltip right>
                <template v-slot:activator="{ on }">
                  <v-btn
                    icon
                    large
                    v-on="{
                      ...on,
                      click: cleanTodo
                    }"
                  >
                    <v-icon>mdi-trash-can-outline</v-icon>
                  </v-btn>
                </template>
                <span>Clear</span>
              </v-tooltip>
            </template>
          </status-box>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
// https://developer.mozilla.org/ja/docs/Web/API/HTML_Drag_and_Drop_API
// https://developer.mozilla.org/ja/docs/DragDrop/Drag_Operations
// https://jp.vuejs.org/v2/guide/single-file-components.html
// import Card from "../components/Card.vue";
import StatusBox from "../components/StatusBox.vue"
export default {
  name: "Kanban",
  data() {
    return {
      newText: "",
      movingCard: undefined
    };
  },
  components: {
    // Card,
    StatusBox
  },
  methods: {
    addTodo() {
      this.$store.commit("addTodo", this.newText);
      this.newText = "";
    },
    cleanTodo() {
      this.$store.commit("cleanTodo");
    },
    moveCard(todo) {
      this.movingCard = todo.name;
    },
    dropCard(status) {
      this.$store.commit("changetaskstate", {
        target: this.movingCard,
        status: status
      });
      this.movingCard = "";
    }
  },
  computed: {
    tasks() {
      return this.$store.state.tasks;
    },
    todo() {
      return this.$store.getters.todo;
    },
    done() {
      return this.$store.getters.doneTodo;
    },
    doing() {
      return this.$store.getters.doingTodo;
    }
  }
};
</script>
<style scoped>
.wrapper {
  display: flex;
}
.add-btn {
  padding-top: 10px;
}
</style>
