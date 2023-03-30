<script>
    import { createEventDispatcher } from 'svelte';
	import { clickOutside } from "../utils/clickOutside";

    const dispatch = createEventDispatcher();

    export let categories;
    export let category = 'General';
    
    let newCategory = '';
    let addNew = false; // Add new category
    let open = false; // Open category select
    let editCategories = false; // editing catergories list
    
    function changeCategory(category){
        dispatch('changeCategory', {
            category: category
        });
    }
    
    function removeCategory(category){
        editCategories = false;
        
        dispatch('removeCategory', {
            category: category
        });
    }
    
    function reset() {
        newCategory = '';
        addNew = false;
        open = false;
        editCategories = false;
    }

    function submitCategory(){
        categories = [...categories, newCategory];
        
        dispatch('changeCategory', {
            category: newCategory
        });
        
        reset();
    }
    
</script>

<button 
class="category-container"
class:open={open}
on:click={() => open = !open }
>
    <div>{category}</div>
    
    {#if open}
    
    <div 
        use:clickOutside 
        on:outclick={reset} 
        class="category-select">
        
        {#each categories as category}
        
            
            <div class:edittable={editCategories}>
                
                
                
                {#if editCategories}
                <input
                on:click|stopPropagation
                value={category}
                on:keyup|stopPropagation ={ e => {
                    if (e.key === 'Enter') {} // update Category }
                    if (e.key === 'Escape') { reset(); }
                }}
                />
                <button 
                    style="margin: 0;"
                    class="remove-option" 
                    on:click={ () => removeCategory(category)}>
                
                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-trash3" viewBox="0 0 16 16">
                        <path d="M6.5 1h3a.5.5 0 0 1 .5.5v1H6v-1a.5.5 0 0 1 .5-.5ZM11 2.5v-1A1.5 1.5 0 0 0 9.5 0h-3A1.5 1.5 0 0 0 5 1.5v1H2.506a.58.58 0 0 0-.01 0H1.5a.5.5 0 0 0 0 1h.538l.853 10.66A2 2 0 0 0 4.885 16h6.23a2 2 0 0 0 1.994-1.84l.853-10.66h.538a.5.5 0 0 0 0-1h-.995a.59.59 0 0 0-.01 0H11Zm1.958 1-.846 10.58a1 1 0 0 1-.997.92h-6.23a1 1 0 0 1-.997-.92L3.042 3.5h9.916Zm-7.487 1a.5.5 0 0 1 .528.47l.5 8.5a.5.5 0 0 1-.998.06L5 5.03a.5.5 0 0 1 .47-.53Zm5.058 0a.5.5 0 0 1 .47.53l-.5 8.5a.5.5 0 1 1-.998-.06l.5-8.5a.5.5 0 0 1 .528-.47ZM8 4.5a.5.5 0 0 1 .5.5v8.5a.5.5 0 0 1-1 0V5a.5.5 0 0 1 .5-.5Z"/>
                    </svg>
                </button>
                
                {:else}
                    <button 
                    class="option" 
                    on:click={() => changeCategory(category)}>
                    {category}
                    </button>
                {/if}
                
            </div>
            {:else}
            <div>No categories</div>
            
        {/each}
            
        <div class="add-option">
            
            {#if addNew}
                <input
                style="margin-top: 8px"
                on:click|stopPropagation
                bind:value={newCategory}
                on:keyup|stopPropagation ={ e => {
                    if (e.key === 'Enter') { submitCategory(); }
                    if (e.key === 'Escape') { reset(); }
                }}
                />
                <button 
                    class="cancel"
                    on:click|stopPropagation={() => {addNew = false;}}
                >
                        Cancel
                </button>
                <!-- Thining of adding cancel button -->
            {:else}
            
                {#if !editCategories}
                <button 
                    on:click|stopPropagation={() => {addNew = true; editCategories = false;}}
                >
                        ADD NEW
                </button>
                {/if}
            
                <button 
                    on:click|stopPropagation={() => {editCategories = !editCategories;}}
                >
                    {editCategories ? 'Done' : 'Edit Categories'}
                </button>
            
            {/if}
        </div>
    </div>
        {/if}
    </button>
    
    <style>
    
        .category-container{
            display: flex;
            flex-direction: column;
            align-items: center;
            position: relative;
            background-color: #fff;
            border: 1px solid #ccc;
            border-radius: 4px;
            padding: 8px;
            width: 100px;
        }
        
        .category-select{
            display: flex;
            flex-direction: column;
            position: absolute;
            z-index: 10;
            background-color: #fff;
            border: 1px solid #ccc;
            border-radius: 4px;
            padding: 8px;
            top: 0;
            left: 0;
            width: 150px;
        }
        
        .category-select div{
            margin-bottom: 8px;
        }
        .category-select .edittable{
            display: flex;
            gap: 0.5em;
            align-items: center;
            
        }
        
        .category-select .option, .remove-option{
            background-color: #fff;
        }
        .category-select .option{
            display: flex;
            flex: 3;
            justify-content: center;
            align-items: center;
            width: 100%;
            background-color: #fff;
            border: 1px solid #ccc;
            border-radius: 4px;
            padding: 8px;
            cursor: pointer;
        }
        
        .remove-option{
            background-color: transparent;
            border: none;
            cursor: pointer;
            
        }
        
        .category-select .option:hover, .remove-option:hover{
            background-color: #eee;
        }
        
        .add-option{
            border-top: 1px solid #ccc;
            margin-top: 8px;
            text-align: center;
            color: #464646;
            cursor: pointer;
        }
        
        .add-option button{
            background: none;
            border: none;
            padding: 8px;
            margin-top: 8px;
            border-radius: 4px;
            cursor: pointer;
        }
        
        button.cancel{
            width: 100%;
            background-color: rgb(255, 116, 116);
        }
        
        input{
            background-color: #fff;
            border: 1px solid #ccc;
            border-radius: 4px;
            padding: 8px;
            /* margin-top: 8px; */
            
            width: 100%;
        }
        
        
        
        
        </style>