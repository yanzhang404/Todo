<template>
  <div id="app">
    <h1 align="center">
      ToDo List
    </h1>
      <div align="center">
          <input type="text" v-model="newTask" placeholder="请输入你的任务名称">
          <button @click="addTask">添加</button>
      </div>
      
      <div>
        <ul>
            <li v-for="task in tasks" :key="task.id" :class="{ completed: task.completed }">
                <span @click="toggleCompletion(task)">{{ task.name }}</span>
                <button @click="deleteTask(task)">删除</button>
            </li>
        </ul>
      </div>
  </div>
</template>

<script>

import axios from 'axios';

    export default{
    
        name: 'App',
        data() {
          return{
            newTask: '',
            tasks: []
          }
        },

        created() {
        this.fetchTasks();
        },  

        methods: {
            addTask() {
                if (this.newTask.trim()) {
                    const task = { name: this.newTask.trim(), completed: false };
                    axios.post('/api/tasks', task)
                        .then(response => {
                            if (response.data.code === 200) {
                                this.newTask = '';
                                this.fetchTasks();  // 重新获取任务列表
                            } else {
                                console.error(response.data.msg);
                            }
                        })
                        .catch(error => {
                            console.error(error);
                        });
                }
            },
            toggleCompletion(task) {
                task.completed = !task.completed;
                axios.put(`/api/tasks/${task.id}`, task)
                    .then(response => {
                        if (response.data.code !== 200) {
                            console.error(response.data.msg);
                        }
                    })
                    .catch(error => {
                        console.error(error);
                    });
            },

            fetchTasks() {
                axios.get("/api/tasks")
                    .then(response => {
                        if (response.data.code === 200) {
                            this.tasks = response.data.data;
                        } else {
                            console.error(response.data.msg);
                        }
                        })
                    .catch(error => {
                    console.error(error);
                    });
            },
            deleteTask(task) {
                axios.delete(`/api/tasks/${task.id}`)
                    .then(response => {
                        if (response.data.code === 200) {
                            this.fetchTasks();  // 重新获取任务列表
                        } else {
                            console.error(response.data.msg);
                        }
                    })
                    .catch(error => {
                        console.error(error);
                    });
        }
        }
      }
</script>


<style>
       h1{
          font-size: 70px;
          color:#059565;
          position: relative;
       }
       body {
            font-family: 'Arial', sans-serif;
            background-color: #F7F9FC;
            padding: 20px;
        }

        li {
            color: #333;
            cursor: pointer;
            padding: 20px;
            border-radius: 3px;
            transition: background-color 0.3s ease;
            position: relative;
            font-weight: bolder;
            font-size: 25px;
           
        }

        li:hover {
            background-color: #E4E7ED;
        }

        .completed {
            color: #A5ACB3;
            text-decoration: line-through;
        }

        input[type="text"], button {
            padding: 10px;
            border: 1px solid #D4D9E1;
            border-radius: 4px;
            font-size: 16px;
            margin-right: 10px;
        }

        button {
            background-color: hsl(171, 82%, 29%);
            color: #FFF;
            cursor: pointer;
            transition: background-color 0.3s ease;
            position: absolute;
            
            
        }

        button:hover {
            background-color: #357ABD;
        }
    </style>
