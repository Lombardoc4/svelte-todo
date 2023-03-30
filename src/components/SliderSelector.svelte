
<script>
    import { createEventDispatcher, afterUpdate } from 'svelte';

    const dispatch = createEventDispatcher();
    
    export let filterOptions = [];
    export let filter = filterOptions[0];
    export let vertical = false;
    
    let btnGroup; // Reference var
    let activeButton = null;
    let overlayPos = {
        left: 0,
        width: 0,
        height: 0,
        top: 0,
    }
    
    function setPostion (button = activeButton) {
        console.log('setPostion', activeButton);
        if (vertical) {
            overlayPos.left=  0;
            overlayPos.top = button.offsetTop + 'px';
            overlayPos.width = '100%';
            overlayPos.height = button.offsetHeight + 'px';
        } else {
            overlayPos.top = 0;
            overlayPos.left = button.offsetLeft + 'px';
            overlayPos.width = button.offsetWidth + 'px';
            overlayPos.height = '100%'
        }
    }
    
    
    afterUpdate(() => {
        if (filterOptions.length === 0) {
            return;
        }
        
        activeButton = btnGroup.querySelector('button.active');
        setPostion();
    });
    
    
    
    function selectFilter (event) {
        filter = event.target.innerHTML;
        
        dispatch('selectFilter', {
            val: filter
        });
    }
    
    
</script>


<div bind:this={btnGroup} class="btn-group" class:vertical={vertical}>
    
    {#each filterOptions as option}
        <button class:active={filter === option} on:click={selectFilter}>{option}</button>
    {/each}
    
    <div class="overlay" style="--top: {overlayPos.top}; --left: {overlayPos.left}; --width: {overlayPos.width}; --height: {overlayPos.height} "/>
</div>



<style>
    .btn-group {
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 8px;
        position: relative;
        background-color: transparent;
    }
    
    .btn-group.vertical {
        flex-direction: column;
    }
    
    .overlay {
        position: absolute;
        z-index: 0;
        
        top: var(--top);
        left: var(--left);
        width: var(--width);
        height: var(--height);
        min-width: 10px;
        background-color: rgba(0,0,0,0.2);
        transition: left 0.2s ease, width 0.2s ease, top 0.2s ease, height 0.2s ease;
        border-radius: 4px;
    }
    
    button{
        width: 100%;
        background: none;
        border: none;
        padding: 8px;
        border-radius: 4px;
        cursor: pointer;
        text-transform: capitalize;
        z-index: 1;
    }
    
    button:hover, button.active{
        background-color: #ccc;
    }
    
    button.active{
        background-color: #666666;
        color: #fff;
    }
</style>