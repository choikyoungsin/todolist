<template>
    <div class="abcd">
        <h2>TODO-List</h2>
        <button class="btn btnmb1" @click="moveToCreatePage">CREATE TODO</button>
        <input type="text" placeholder="검색" class="form-control" v-model="serchText">
        <!-- <TodoSimpleForm  @add-todo="addTodo"/> -->
        <div v-if="!todos.length">
            찾는 문장이 없습니다..
        </div>
        <TodoList :todos="todos" @delete-todo="deleteTodo" @toggle-todo="toggleTodo" />
        <br>
        <nav class="nav">
            <ul class="pagination">
                <li v-if="currentPage !==1" class="page-item">
                  <a href="#!" class="page-link"  @click="getTodos(currentPage-1)"><i class="fa-solid fa-chevron-left"></i></a>
                </li>
               
                <li v-for="page in numberofPages" :key="page" class="page-item" :class="currentPage===page ? 'active' : ''">
                  <a href="#!" @click="getTodos(page)" class="page-link" :class="currentPage===page ? 'active' : ''">{{ page }}</a>
                </li>
  
                <li v-if="numberofPages !==currentPage" class="page-item" ><a href="#!" @click="getTodos(currentPage+1)" class="page-link"><i class="fa-solid fa-chevron-right"></i></a>
                </li>
            </ul>
        </nav>
    </div>
  </template>
  
  <script>
  import { watch, computed, ref } from 'vue';
  /* import TodoSimpleForm from '@/components/TodoSimpleForm.vue'; */
  import TodoList from '@/components/TodoList.vue';
  import axios from 'axios';
  import { useRouter } from 'vue-router'
  
  export default {
    components: {
        /* TodoSimpleForm, */
        TodoList,
    },
    setup(){
        /* 버튼 라우터 */
        const router=useRouter();
        const todos = ref([ ]);
        const serchText= ref('');
        const error=ref('');
        const limit=5;
        const numberOfTodos = ref(0);
        const currentPage=ref(1);
        const numberofPages=computed(() =>{
            return Math.ceil(numberOfTodos.value/limit)
        });
  
        const getTodos = async (page = currentPage.value) =>{
            currentPage.value=page;
            try{
                const res=await axios.get(`http://localhost:3000/todos?_sort=id&_order=desc&subject_like=${serchText.value}&_page=${page}&_limit=${limit}`);
                //console.log(res.headers)
                numberOfTodos.value=res.headers['x-total-count'];
                todos.value=res.data;
            }
            catch (err){
                console.log(err);
                error.value="찾는 문장이 없습니다"
            }
        };
        getTodos();

        const moveToCreatePage = ()=>{
            router.push({
                name:'TodoCreate',
            })
        }
        const addTodo = async (todo) => {
            error.value='';
            try{
                await axios.post('http://localhost:3000/todos', {
                    subject:todo.subject,
                    completed:todo.completed, 
                });
                getTodos(1);
                // todos.value.push(res.data);
            }catch (err){
                console.log(err);
                error.value="잘못된 입력입니다."
            }
            
        }
        watch(serchText, () => {
          // console.log(current, prev)
          getTodos(1);
        })
  
        /* const addTodo = (todo) => {
           // todos.value.push(todo);
            error.value='';
           axios.post('http://localhost:3000/todos',{
                subject:todo.subject,
                completed:todo.completed,
           }).then((res) =>{
                console.log(res);
                todos.value.push(res.data)
           }).catch((err) =>{
                console.log(err);
                error.value="잘못된 입력입니다."
           });
        } */
       
        const deleteTodo = async (index) => {
            error.value="";
            const id= todos.value[index].id;
            try{
                await axios.delete('http://localhost:3000/todos/'+ id);
                // todos.value.splice(index, 1);
                getTodos(1);
  
            }catch(err){
                console.log(err);
                error.value="찾는 문장이 없습니다"
            }
           
        }
        const toggleTodo= async (index, checked) =>{
           // console.log(index)
           error.value="";
           const id= todos.value[index].id;
  
           try{
                await axios.patch('http://localhost:3000/todos/'+ id, {
                    completed: checked/*  !todos.value[index].completed */
                });
                todos.value[index].completed= checked/* !todos.value[index].completed; */
           }catch(err){
                console.log(err);
                error.value="찾는 문장이 없습니다"
           }
        }
        // const filteredTodos = computed(() =>{
        //     if(serchText.value){
        //         return todos.value.filter(todo =>{
        //             return todo.subject.includes(serchText.value);
        //         })
        //     }
        //     return todos.value;
        // })
  
        return {
            todos,
           /*  todoStyle, */
           deleteTodo,
           addTodo,
           toggleTodo,
           serchText,
           /* filteredTodos */
           getTodos,
           numberofPages,
           currentPage,
           moveToCreatePage,
        }
    }
  }
  </script>
  
  <style>
    *{margin: 0; padding: 0; box-sizing: border-box;}
    .container{width: 100%; max-width: 1024px; margin: 0 auto; padding: 0 20px;}
    .abcd div{
        text-align: center;  font-weight: bold;
    }
    h2{text-align: center; color: #147f8d; padding:50px 0;}
    .d-flex{display: flex;}
    .flex-grow-1{flex-grow: 1;}
    .flex-grow-1 input{width: 98%; padding: 10px 20px;}
    .btn{padding: 10px 20px; border: none; background: #147f8d; color: #fff; width:50%; margin-bottom:20px; margin-left:25%;}
    form{margin-bottom: 50px;}
    .card{margin: 10px 0;}
    .card-body{border: 1px solid #ccc; padding: 10px 20px; display: flex; justify-content: space-between; align-items: center;}
    .form-check-input{margin-right: 10px;}
    .todoStyle{text-decoration: line-through;color: gray}
    .btnR{padding: 5px 20px; background: #e93838; color: #fff; border: none;}
    .form-control{width: 50%; border: 1px solid #ddd; margin-bottom: 10px; padding: 10px 20px; text-align: center; margin-left:25%;}
    .pagination{list-style-type: none; display: flex; justify-content: center;}
    .page-item{ padding: 10px; margin: 0 3px; border: 1px solid #ddd}
    .page-link{color: #666; text-decoration: none;}
    .active{background: #666; color: #fff}
    .btnmb{margin-bottom: 10px;}
  </style>