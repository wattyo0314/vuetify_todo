<template>
  <v-app>
    <v-container>
      <v-row justify="center">
        <v-col>
          <v-app-bar color="light-blue" light>
            <v-toolbar-title>Vue.jsでTODOアプリ</v-toolbar-title>
          </v-app-bar>
          <v-card>
            <v-form v-on:submit.prevent="addTasks">
              <v-card-title>タイトル</v-card-title>
              <v-card-text>
                <v-text-field
                  label="タスクを入力してください"
                  v-model="addTitle"
                  :rules="[required, limit_length]"
                  counter="25"
                  ref="observer"
                >
                </v-text-field>
              </v-card-text>
              <v-card-title>期限</v-card-title>
              <v-card-text>
                <v-text-field type="date" v-model="deadline"> </v-text-field>
              </v-card-text>
              <v-divider></v-divider>
              <v-card-actions>
                <v-btn type="submit" @click="submit">送信する</v-btn>
                <span v-if="success">送信成功！</span>
              </v-card-actions>
            </v-form>
          </v-card>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-simple-table>
            <thead>
              <tr>
                <th>ID</th>
                <th>タイトル</th>
                <th>期限</th>
                <th>削除</th>
                <th>編集</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(todo, index) in todos" v-bind:key="todo.id">
                <td>{{ todo.id }}</td>
                <td>{{ todo.title }}</td>
                <td>{{ todo.deadline }}</td>
                <td @click="removeTask"><v-icon>mdi-delete</v-icon></td>
                <td @click="editTask(index)">
                  <v-icon>mdi-account-edit</v-icon>
                </td>
              </tr>
            </tbody>
          </v-simple-table>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      todos: [],
      addTitle: "",
      deadline: null,
      success: false,

      required: (value) => !!value || "必ず入力してください", // 入力必須の制約
      limit_length: (value) =>
        value.length <= 25 || "25文字以内で入力してください", // 文字数の制約
    };
  },
  created: function () {
    this.deadline = this.formatDate(new Date());
  },
  methods: {
    formatDate: function (dt) {
      const y = dt.getFullYear();
      const m = ("00" + (dt.getMonth() + 1)).slice(-2);
      const d = ("00" + dt.getDate()).slice(-2);
      const result = y + "-" + m + "-" + d;
      return result;
    },
    addTasks: function () {
      if (!this.addTitle.length) {
        return;
      }
      this.todos.push({
        id: this.getUniqueStr(this.todo),
        title: this.addTitle,
        deadline: this.deadline,
      });
      this.addTitle = "";
    },
    removeTask: function (todo) {
      if (confirm("本当に削除しますか？")) {
        const todoIndex = this.todos.indexOf(todo);
        this.todos.splice(todoIndex, 1);
      }
    },
    getUniqueStr: function (myStrong) {
      let strong = 1000;
      if (myStrong) strong = myStrong;
      return (
        new Date().getTime().toString(16) +
        Math.floor(strong * Math.random()).toString(16)
      );
    },
    editTask(id) {
      let newTitle = window.prompt("以下内容で更新します。");
      if (newTitle === "") {
        alert("入力欄が空欄です。");
        return;
      }
      this.edit(id, newTitle);
    },
    edit(index, title) {
      this.todos[index].title = title;
    },
    changeToDo(id) {
      let changeIndex = "";
      this.todos.some(function (value, index) {
        if (value.id === id) {
          changeIndex = index;
        }
      });
      this.todos[changeIndex].flag = this.change(changeIndex);
    },
    change(changeIndex) {
      if (this.todos[changeIndex].flag === true) {
        return false;
      } else {
        return true;
      }
    },
    submit() {
      if (this.$refs.observer.validate()) {
        this.success = true;
      } else {
        this.success = false;
      }
    },
  },
};
</script>
