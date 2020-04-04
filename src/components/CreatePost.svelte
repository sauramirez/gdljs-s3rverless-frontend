<script>
import TextForm from './TextForm.svelte';
import ImageForm from './ImageForm.svelte';

let type = 'text';
</script>

<button on:click={() => type = 'text'}>Text</button>
<button on:click={() => type = 'image'}>Image</button>
    {#if type === 'text'}
<TextForm />
    {:else}
<ImageForm />
    {/if}
