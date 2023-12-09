<template>
	<main class="app">
		<section class="greeting">
			<h1 class="title">
				What's Up! 
				<input type="text" placeholder="이름 입력" v-model.trim.lazy="name">
				{{ name }}
			</h1>
		</section>
		<section class="create_todo">
			<h2>CREATE A TODO</h2>
      <form id="new-todo-form" @:submit.prevent="addTodo">
          <h4>할일을입력해주세요</h4>
          <input type="text" placeholder="할일을입력해주세요" class="todo_input"
                  v-model="inputContent">
        <div class="options">
          <label class="label">
            <input type="radio" name="category" id="category1" value="business"
                v-model="inputCategory">
                <span class="bubble business"></span>
                <div>Business</div>
          </label>
          <label class="label">
            <input type="radio" name="category" id="category2" value="personal"
                v-model="inputCategory">
                <span class="bubble personal"></span>
                <div>Personal</div>
          </label>
          선택한 카테고리 : {{ inputCategory }}
        </div>
        <input type="submit" value="Add Todo">
      </form>
		</section>

		<section class="todo_list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div :class="`todo_item ${todo.done && 'done'}`" v-for="todo in todos_asc">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo_content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
		</section>
	</main>
</template>

<script setup>
import { ref, watch, onMounted, computed } from 'vue';

	const name = ref('');
  const inputContent = ref(null);
  const inputCategory = ref(null);
  const todos = ref([]);

  const todos_asc = computed(() => 
                      todos.value.sort((a, b) => 
                      b.createAt - a.createAt)
                    );
  const removeTodo = (item) => {
    todos.value = todos.value.filter((aa) => aa !== item)
  };

  // 할일입력값들을 받아오는 함수
  const addTodo = () => {
    if(inputCategory.value.trim() === '' || inputCategory.value === null) {
      return false;
    }

    todos.value.push({
      content:inputContent.value,
      category:inputCategory.value,
      done:false,
      createAt:new Date().getTime()
      // 시간순으로 순서를 넣기위해 UTC 시간을 받아옴
    });

    // 입력후초기화
    inputContent.value = '';
    inputCategory.value = '';
  };
  
  watch(todos, (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal));
  }, {deep:true
  });
  // deep : 속성의 프로퍼티나 하위뎁스도 감지

	watch(name,(newName)=>{
		localStorage.setItem('name', newName)
  });

	onMounted(()=>{
    name.value = localStorage.getItem('name') || '';
    //todos.value = localStorage.getItem('todos') || [];
    todos.value = JSON.parse(localStorage.getItem('todos')) || [];
  });
</script>
