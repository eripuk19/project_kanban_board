<script>
    import { flip } from 'svelte/animate';
  
    export let title;
    export let items = [];
    export let onDrop;
    export let onDragStart;
    export let dragOver;
  </script>
  
  <!-- svelte-ignore a11y_no_static_element_interactions -->
  <section class="p-3 bg-white h-[400px] w-[300px] space-y-2"
    on:dragover={dragOver}
    on:drop={onDrop}
  >
    <h2>{title}</h2>
    {#each items as item (item)}
      <article
        draggable="true"
        on:dragstart={(event) => onDragStart(item, event)}
        class="p-4 bg-pink-200 cursor-grab"
        animate:flip
      >
        {item}
      </article>
    {/each}
  </section>
  