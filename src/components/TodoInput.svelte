<script>
    import { createEventDispatcher } from 'svelte';
	import CategoryInput from './CategoryInput.svelte';

    const dispatch = createEventDispatcher();
    
    export let categories;
    export let category = 'General';
    
    export let todo = '';
    
    
    function cancel(){
        dispatch('cancel');
    }
    
    function save(){
        // Show error if no text is entered
        if (todo.length <= 0) {
            return;
        }
        
        // Save catergory if it doesn't exist
        if (categories.indexOf(category) === -1) {
            dispatch('submitCategory', {
                category: category
            });
        }
        
        // Save new todo
        dispatch('saveTodo', {
            text: todo,
            category: category
        });
        
        // Reset inputs
        todo = '';
        category = 'General';
    }
    
    function changeCategory(event) {
        category = event.detail.category;
    }
    
</script>
    <input
    class="todo-input"
    bind:value={todo}
    on:keyup={ e => {
        if (e.key === 'Enter') { save(); }
        if (e.key === 'Escape') { cancel(); }
    }}/>
    
    <CategoryInput 
        {categories}
        bind:category
        on:changeCategory={changeCategory} 
        on:removeCategory 
    />
    
    <!-- Save Button -->
    <button class="icon" on:click={save} >
        <svg 
        fill={todo.length >= 1 ? "#fff": "currentColor"} 
        style="cursor: pointer; background-color: {todo.length >= 1 ? "#0969da": "#fff"}; border-radius: 50%;"
        xmlns="http://www.w3.org/2000/svg" 
        width="16" 
        height="16" 
        class="bi bi-arrow-up-circle" 
        viewBox="0 0 16 16">
            <path fill-rule="evenodd" d="M1 8a7 7 0 1 0 14 0A7 7 0 0 0 1 8zm15 0A8 8 0 1 1 0 8a8 8 0 0 1 16 0zm-7.5 3.5a.5.5 0 0 1-1 0V5.707L5.354 7.854a.5.5 0 1 1-.708-.708l3-3a.5.5 0 0 1 .708 0l3 3a.5.5 0 0 1-.708.708L8.5 5.707V11.5z"/>
        </svg>
    </button>

    
<style>
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
    .todo-input{
        display: flex;
        flex-direction: column;
        position: relative;
        background-color: #fff;
        border: 1px solid #ccc;
        border-radius: 4px;
        padding: 8px;
        width: 100%;
    }
</style>