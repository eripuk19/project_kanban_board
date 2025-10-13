<script>
  import { flip } from 'svelte/animate';

  export let title;
  export let items = [];
  export let onDrop;
  export let onDragStart;
  export let dragOver;
  export let storyPointsSum = 0;
</script>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<section
  class="p-3 bg-white h-[550px] w-[300px] space-y-2"
  on:dragover={dragOver}
  on:drop={onDrop}
>
  <h2 class="flex justify-between items-center">
    <span>{title}</span>
    <span class="text-pink-600 font-semibold">SP: {storyPointsSum}</span>
  </h2>

  {#each items as item (item.id)}
    <article
      draggable="true"
      on:dragstart={(event) => onDragStart(item, event)}
      class="p-4 bg-pink-200 cursor-grab"
      animate:flip
    >
      <strong>{item.title}</strong><br />
      Priority: {item.priority}<br />
      Due: {item.dueDate}<br />
      Story Points: {item.storyPoints}
    </article>
  {/each}
</section>
