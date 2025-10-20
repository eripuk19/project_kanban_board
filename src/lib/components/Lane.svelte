<script>
  import { flip } from 'svelte/animate';

  export let title;
  export let items = [];
  export let onDrop;
  export let onDragStart;
  export let dragOver;
  export let storyPointsSum = 0;

  function shareIssue(item) {
    if (navigator.share) {
      navigator.share({
        title: item.title,
        text: `Issue: ${item.title}\nDescription: ${item.description}\nDue: ${item.dueDate}\nStory Points: ${item.storyPoints}\nPriority: ${item.priority}`,
      }).catch(err => console.error('Error sharing:', err));
    } else {
      alert('Web Share API not supported on this browser.');
    }
  }
</script>

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
      class="p-4 bg-pink-200 cursor-grab flex flex-col space-y-2"
      animate:flip
    >
      <div>
        <strong>{item.title}</strong><br />
        Priority: {item.priority}<br />
        Due: {item.dueDate}<br />
        Story Points: {item.storyPoints}
      </div>
      <button
        on:click={() => shareIssue(item)}
        class="mt-2 self-start bg-blue-500 text-white px-2 py-1 rounded hover:bg-blue-600 text-sm"
      >
        📤 Share
      </button>
    </article>
  {/each}
</section>
