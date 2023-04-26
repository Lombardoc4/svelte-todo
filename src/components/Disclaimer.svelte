<script>
	import { onMount } from "svelte";

    let active = false;
    // on load check if disclaimer has been accepted
    onMount(() => {
        const accepted = window.sessionStorage.getItem('cl-svelte-disclaimer');
        const valid = JSON.parse(accepted);
        // if not accepted, show disclaimer
        active = !valid;
    });

    const confirm = () => {
        if (active) {
            active = false;
            window.sessionStorage.setItem('cl-svelte-disclaimer', 'true');
        }
    }
    
</script>
<svelte:window on:keydown={confirm} on:click={confirm}/>

<div class="disclaimer" class:active={active}>
    <div class="modal">
        <div class="header">
            <h1>Disclaimer: </h1>
            <h2><i>In Progress</i></h2>
        </div>
        <p>
            This app is in dev mode and still need some work,
            but feel free to play around with it.
            Items are stored in local storage
            <br/><br/>
            <b>Press anything to close this</b>
        </p>
    </div>
</div>

<style>
    .disclaimer {
        overflow: hidden;
        height: 100vh;
        width: 100vw;
        position: fixed;
        top: 0;
        left: 0;
        z-index: 100;
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: transparent;
        pointer-events: none;
        
    }
    
    .active{
        background-color: rgba(0,0,0,0.5);
        pointer-events: initial;
    }
    
    .active .modal{
        opacity: 1;
        transform: translateY(0);
    }
    
    .modal{
        transform: translateY(-100%);
        opacity: 0;
        padding: 1.5em 2em;
        background-color: #f9f9f9;
        max-width: 300px;    
        border-radius: 1em;
        transition: transform 0.3s ease, opacity 0.3s ease;
    }
    
    .header{
        margin-bottom: 1em;
    }
</style>