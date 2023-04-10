<script>
	import { createEventDispatcher, onMount } from "svelte";
    const dispatch = createEventDispatcher();
    

    export let notes = [];
    let addNew = false;
    let newNote = {
        title: '',
        content: ''
    };
    
    function saveNote() {
        newNote.id = Date.now();
        
        dispatch('saveNote', {
            note: newNote
        });
        
        newNote = {
            title: '',
            content: ''
        };
        
        addNew = false;
    }
    
    
</script>

<div class="notes">
    <div>
        <h2>Notes</h2>
        <button on:click={() => addNew = !addNew}>Add Note</button>
    </div>
    
    {#if addNew}
        <label for="noteTitle">Title</label>
        <input type="text" id="noteTitle" placeholder="Title"/>
        
        <label for="noteContent">Content</label>
        <textarea id="noteContent"/>
        
        <button on:click={saveNote}>Save</button>
    {/if}
    
    {#each notes as note}
        <div class="note">
            <h3>{note.title}</h3>
            <p>{note.content}</p>
        </div>
    {/each}
    
</div>