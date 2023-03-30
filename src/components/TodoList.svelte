<script>
    import { onMount, createEventDispatcher } from 'svelte';
	import SliderSelector from './SliderSelector.svelte';
	import TodoInput from './TodoInput.svelte';
    import { fade } from 'svelte/transition';
    
    const dispatch = createEventDispatcher();

    export let categories = [];
    export let category = 'General';
    
    let allTodos = [];
    let filterOptions = ['active', 'completed', 'all'];
    let filter = filterOptions[0];
    let editIndex = -1;
    
	onMount(async () => {
        allTodos = JSON.parse(window.localStorage.getItem('todos')) || [];
	});

    // Definitely can simplify
    $: todos = allTodos.length >= 0 ? allTodos.map((todo, i) => ({...todo, index: i}))
    .filter((todo) => {
        if (category) {
            return todo.category === category;
        }
    }).filter((todo) => {
        if (filter === 'active') {
            return !todo.done;
        }
        if (filter === 'all') {
            return true;
        }
        if (filter === 'completed') {
            return todo.done;
        }
    }).reverse() : [];
    
    $: remaining = todos.length >= 0 ? todos.filter(t => !t.done).length : 0;


	function saveTodo(values, index) {
        // if index is undefined, then we are creating a new todo
        if (index === undefined) {
            const newTodo = { done: false, ...values };
            allTodos = [...allTodos, newTodo];
            
            //  Set category filter or input category
            category = values.category;
        }
        
        // if index is defined, then we are editing an existing todo
        if (index) {
            allTodos[index] = {...allTodos[index], ...values};
            
            // Set editing to false
            editIndex = -1;
        }
        
        window.localStorage.setItem('todos', JSON.stringify(allTodos));
	}
    
    function removeTodo(index) {
        // Remove Category if no more of same category
        // Need to go first since we need to get the category before we remove the todo
        let sameCategory = allTodos.filter(todo => todo.category === allTodos[index].category)  
        if ( sameCategory.length <= 0 ){
            dispatch('removeCategory', { category: allTodos[index].category })
        }
        
        // Update todos
        allTodos = allTodos.filter((todo, i) => i !==  index);
        window.localStorage.setItem('todos', JSON.stringify(allTodos));
    }
    
    // Move todos to General if category is removed
    function migrateCategory(event) {
        const { category } = event.detail;
        
        dispatch('removeCategory', { category })
        
        // 
        allTodos = allTodos.map((todo) => {
            if (todo.category === category) {
                todo.category = 'General';
                // Let user select category per task?
            }
            return todo;
        });
        
        window.localStorage.setItem('todos', JSON.stringify(allTodos));
    }

    function changeFilter(event) {
        filter = event.detail.val;
    }
    
    function editTodo(index = -1) {
        editIndex = index;
    }

</script>


<h2 style="margin-bottom: 0.25em;">What needs to get done?</h2>

<!-- Header input -->
<div class="flex-center main-input">
    
    <TodoInput 
    bind:categories
    on:saveTodo={(e) => {saveTodo(e.detail)}} 
    
    on:submitCategory 
    on:removeCategory={migrateCategory} />
    
</div>

<div class="subheader">
    <div>
        <h2>{category}</h2>
        <em>{remaining} remaining</em>
    </div>
    
    <!-- Inner Filters -->
    <SliderSelector 
    {filterOptions} 
    {filter} 
    on:selectFilter={changeFilter}/>
    
</div>

<div class="todo-list">
    {#each todos as todo, index}
        
    <div class:done={todo.done} in:fade={{delay: 600}} class="todo">
                
        <button class="icon" on:click={() => {saveTodo({done: !todo.done}, todo.index)}}>
            <input
            type=checkbox
            bind:checked={todo.done}
            >
        </button>
        
        <!-- If not editting -->
        {#if editIndex !== index}
            <span class="value" on:dblclick={() => editTodo(index)}>{todo.text}</span>
            
            <!-- If done don't show edit button -->
            {#if !todo.done}
                <button class="icon" on:click={() => editIndex = index} >
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-pencil-square" viewBox="0 0 16 16">
                        <path d="M15.502 1.94a.5.5 0 0 1 0 .706L14.459 3.69l-2-2L13.502.646a.5.5 0 0 1 .707 0l1.293 1.293zm-1.75 2.456-2-2L4.939 9.21a.5.5 0 0 0-.121.196l-.805 2.414a.25.25 0 0 0 .316.316l2.414-.805a.5.5 0 0 0 .196-.12l6.813-6.814z"/>
                        <path fill-rule="evenodd" d="M1 13.5A1.5 1.5 0 0 0 2.5 15h11a1.5 1.5 0 0 0 1.5-1.5v-6a.5.5 0 0 0-1 0v6a.5.5 0 0 1-.5.5h-11a.5.5 0 0 1-.5-.5v-11a.5.5 0 0 1 .5-.5H9a.5.5 0 0 0 0-1H2.5A1.5 1.5 0 0 0 1 2.5v11z"/>
                    </svg>
                </button>
            {/if}
            
            <button class="icon" on:click={() => removeTodo(todo.index)}>
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-trash3" viewBox="0 0 16 16">
                    <path d="M6.5 1h3a.5.5 0 0 1 .5.5v1H6v-1a.5.5 0 0 1 .5-.5ZM11 2.5v-1A1.5 1.5 0 0 0 9.5 0h-3A1.5 1.5 0 0 0 5 1.5v1H2.506a.58.58 0 0 0-.01 0H1.5a.5.5 0 0 0 0 1h.538l.853 10.66A2 2 0 0 0 4.885 16h6.23a2 2 0 0 0 1.994-1.84l.853-10.66h.538a.5.5 0 0 0 0-1h-.995a.59.59 0 0 0-.01 0H11Zm1.958 1-.846 10.58a1 1 0 0 1-.997.92h-6.23a1 1 0 0 1-.997-.92L3.042 3.5h9.916Zm-7.487 1a.5.5 0 0 1 .528.47l.5 8.5a.5.5 0 0 1-.998.06L5 5.03a.5.5 0 0 1 .47-.53Zm5.058 0a.5.5 0 0 1 .47.53l-.5 8.5a.5.5 0 1 1-.998-.06l.5-8.5a.5.5 0 0 1 .528-.47ZM8 4.5a.5.5 0 0 1 .5.5v8.5a.5.5 0 0 1-1 0V5a.5.5 0 0 1 .5-.5Z"/>
                </svg>
            </button>
        {:else}
            <!-- Editing, Input with Todo values -->
            <TodoInput 
            bind:categories
            on:saveTodo={(e) => {saveTodo(e.detail, todo.index)}}  
            
            todo={todo.text} 
            category={todo.category} 
            on:cancel={editTodo} 
            
            on:submitCategory 
            on:removeCategory/>
             
            <!-- Cancel button -->
            <button on:click={() => editTodo(-1)} class="icon">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-x-lg" viewBox="0 0 16 16">
                    <path d="M2.146 2.854a.5.5 0 1 1 .708-.708L8 7.293l5.146-5.147a.5.5 0 0 1 .708.708L8.707 8l5.147 5.146a.5.5 0 0 1-.708.708L8 8.707l-5.146 5.147a.5.5 0 0 1-.708-.708L7.293 8 2.146 2.854Z"/>
                </svg>
            </button>
        {/if}
            
    </div>
    
    {:else}
    
    <p in:fade={{delay: 600}} >No Items</p>
        
    {/each}
</div>

<style>
    
    .todo-list{
        display: flex;
        flex-direction: column;
        padding: 1em 0 ;
        gap: 8px;
    }
    
    .todo {
        display: flex;
        align-items: center;
        gap: 8px;
        padding: 0.5em;
        border-radius: 0.25em;
        border: 1px solid #ccc;
    }
    
    .icon{
        background: none;
        border: none;
        padding: 8px;
        border-radius: 4px;
        cursor: pointer;
    }
    
    .icon:hover{
        background: #eee;
    }
    
    .value {
        width: 100%;
    }
    
	.done {
		opacity: 0.4;
        background-color: rgb(132, 225, 113);
	}
    
    .flex-center {
        display: flex;
        align-items: center;
        gap: 8px;
    }
    
    .main-input{
        margin-bottom: 1em;
    }
    
    .subheader{
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
</style>