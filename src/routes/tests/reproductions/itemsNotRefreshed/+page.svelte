<script>
    import Select from '$lib/Select.svelte';
    import Fuse from 'fuse.js';
    import { onMount } from 'svelte';

    const bananas = ['Banana 1', 'Banana 2', 'Banana 3'];
    const apples = ['Apple 1', 'Apple 2', 'Apple 3'];
    
    let selectedFruit = 'bananas';
    let showSelect = true;
    let selectInput;
    let listVisible = false;

    const createOptionsHandler = (items) => {
        return async (filterText) => {
            console.log('loadOptions called with:', { items, filterText }); // Debug handler execution
            if (filterText.length === 0) return [...items];
            const fuse = new Fuse([...items]);
            const results = fuse.search(filterText).map(({ item }) => item);
            console.log('loadOptions returns:', results); // Debug results
            return results;
        };
    };

    $: loadOptions = createOptionsHandler(selectedFruit === 'bananas' ? bananas : apples);
    console.log('loadOptions changed:', selectedFruit); // Debug log for loadOptions change

    $: if (selectedFruit) {
        console.log('Selected fruit changed to:', selectedFruit);
    }

    const sleep = (ms) => new Promise(resolve => setTimeout(resolve, ms));

    const actions = [
        () => listVisible = true,
        () => listVisible = false,
        () => selectedFruit = selectedFruit === 'bananas' ? 'apples' : 'bananas',
        () => listVisible = true,
        () => listVisible = false,
        () => showSelect = false,
        () => showSelect = true
    ];

    async function runSequence() {
        for (const action of actions) {
            action();
            await sleep(1000);
        }
        runSequence(); // Loop
    }

    onMount(() => {
        runSequence();
    });
</script>

<button on:click={() => showSelect = !showSelect}>
    {showSelect ? 'Hide' : 'Show'} Select
</button>


{#if showSelect}
    <Select 
        {loadOptions} 
        debounceWait="0" 
        bind:input={selectInput}
        bind:listOpen={listVisible}
    />
{/if}

<div>
    <label>
        <input type="radio" bind:group={selectedFruit} value="bananas"> Bananas
    </label>
    <label>
        <input type="radio" bind:group={selectedFruit} value="apples"> Apples
    </label>
</div>

