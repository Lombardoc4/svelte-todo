<script>
	import TodoList from "../components/TodoList.svelte";
    import { onMount } from 'svelte';
	import SliderSelector from "../components/SliderSelector.svelte";
	import Notebook from "../components/Notebook.svelte";

    
    let uiView = 'list';
    let categories = [''];
    let category = 'General';
    let loading = true;
    let notes = [];
    
    $: noteTitles = notes.map(note => note.id);
    
	onMount(async () => {
        categories = window.localStorage.getItem('categories') ? JSON.parse(window.localStorage.getItem('categories')) : ['General'];
        notes = window.localStorage.getItem('notes') ? JSON.parse(window.localStorage.getItem('notes')) : [];
        
        
        
        // if (notes.length > 0) {
        //     // loop id and get note from Storage
        //     notes.map(note => {
        //         // note = {
        //         //     id: note.id,
        //         //     note: value = window.localStorage.getItem(note.id) ? JSON.parse(window.localStorage.getItem(note.id)) : ''
        //         // }
                
        //         return;
        //     })
        // }
        
        loading = false;
	});
    
    // TODO - Save Session State - Active category, active filter
    function toggleUI() {
        uiView = uiView === 'list' ? 'notes' : 'list';
    }
    
    function saveNote (note) {
        notes = [...notes, note];
        window.localStorage.setItem('notes', JSON.stringify(notes));
    }
    
    function addCategory(event) {
        categories = [...categories, event.detail.category];
        category = event.detail.category;
        
        window.localStorage.setItem('categories', JSON.stringify(categories));
    }
    
    function removeCategory(event){
        category = categories[categories.indexOf(event.detail.category) - 1];
        categories = categories.filter((cat) => cat !== event.detail.category);
        window.localStorage.setItem('categories', JSON.stringify(categories));
    }
    
    function setCategory(val) {
        if (uiView === 'notes')
            uiView = 'list';
            
            
        category = val;
    }
    
    
</script>

<main>
 
    <div class="sidebar">
        <h1>Welcome</h1>
        {#if !loading}
        
            
            {#if uiView !== 'notes'}
            <button on:click={toggleUI}>
                View Notes
            </button>
            {/if}
            
            {#if categories.length > 1 || uiView === 'notes'}
                <div class="categories">
                    <p>Todos</p>
                    <SliderSelector 
                    bind:filterOptions={categories} 
                    bind:filter={category} 
                    vertical={true}
                    on:selectFilter={(e) => {setCategory(e.detail.val)}}/>
                </div>
            {/if}
        
        {/if}
        
    </div>
    
    <div class="todo-list">
        {#if uiView === 'list'}
            <TodoList 
            bind:category
            bind:categories
            on:submitCategory={addCategory} 
            on:removeCategory={removeCategory}/>
        {/if}
            
            {#if uiView === 'notes'}
                <Notebook
                    on:saveNote={saveNote}
                    bind:notes={notes}
                />
            {/if}
            
    </div>
    
</main>

<style>
    :global(*) {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    
    :global(body) {
        font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    }
    
    main{
        max-height: 100vh;
        width: clamp(340px, 90%, 600px);
        display: flex;
        gap: 2em;
        margin: auto;
        padding: 2em 0;
    }
    
    .sidebar {
        flex: 1;
        display: flex;
        flex-direction: column;
        gap: 8px;
    }
    
    .categories{
        margin-top: 2em;
        display: flex;
        flex-direction: column;
        gap: 8px;
    }
    
    .categories p{
        font-weight: bold;
        border-bottom: 1px solid #ccc;
        padding-bottom: 8px;
        margin-bottom: 8px;
    }
    
    .todo-list {
        flex: 3;
        display: flex;
        flex-direction: column;
    }
</style>