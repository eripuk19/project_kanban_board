<script>
  import { flip } from 'svelte/animate';
  import { createEventDispatcher } from 'svelte';
  import { format, parseISO, isAfter } from 'date-fns';

  export let title;
  export let items = [];
  export let onDrop;
  export let onDragStart;
  export let dragOver;
  export let storyPointsSum = 0;

  const dispatch = createEventDispatcher();

  function shareIssue(item) {
    if (navigator.share) {
      navigator.share({
        title: item.title,
        text: `Issue: ${item.title}\nDescription: ${item.description}\nDue: ${format(parseISO(item.dueDate), 'PPP')}\nStory Points: ${item.storyPoints}\nPriority: ${item.priority}`,
      }).catch(err => console.error('Error sharing:', err));
    } else {
      alert('Web Share API not supported on this browser.');
    }
  }

  function deleteIssue(item) {
    dispatch('deleteIssue', item.id);
  }

  function isOverdue(dueDate) {
    if (!dueDate) return false;
    const today = new Date();
    const due = parseISO(dueDate);
    return isAfter(today, due);
  }

  function formatDate(dateStr) {
    if (!dateStr) return '';
    return format(parseISO(dateStr), 'PPP');
  }

  // --- Export item as ICS calendar event ---
  function exportToCalendar(item) {
    const startDate = item.creationDate ? new Date(item.creationDate) : new Date();
    const endDate = item.dueDate ? new Date(item.dueDate) : new Date(startDate.getTime() + 60 * 60 * 1000);

    const formatDateICS = (date) => date.toISOString().replace(/[-:]/g, '').split('.')[0] + 'Z';

    const icsContent = `
BEGIN:VCALENDAR
VERSION:2.0
PRODID:-//YourKanbanBoard//EN
BEGIN:VEVENT
UID:${item.id}@kanbanboard
DTSTAMP:${formatDateICS(new Date())}
DTSTART:${formatDateICS(startDate)}
DTEND:${formatDateICS(endDate)}
SUMMARY:${item.title}
DESCRIPTION:${item.description || ''}
PRIORITY:${item.priority === 'Critical' ? 1 : item.priority === 'High' ? 2 : item.priority === 'Medium' ? 3 : 5}
END:VEVENT
END:VCALENDAR
`.trim();

    const blob = new Blob([icsContent], { type: 'text/calendar' });
    const url = URL.createObjectURL(blob);

    const link = document.createElement('a');
    link.href = url;
    link.download = `${item.title}.ics`;
    link.click();
    URL.revokeObjectURL(url);
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
      class="p-4 bg-pink-300 cursor-grab flex flex-col space-y-2 relative"
      animate:flip
    >
      {#if isOverdue(item.dueDate)}
        <div class="absolute top-0 left-0 h-full w-1 bg-red-600 rounded-r"></div>
      {/if}
      <div class="ml-2">
        <strong>{item.title}</strong><br />
        Priority: {item.priority}<br />
        Due: {formatDate(item.dueDate)}<br />
        Creation: {formatDate(item.creationDate)}<br />
        Story Points: {item.storyPoints}
      </div>
      <div class="flex space-x-2 mt-2">
        <button
          on:click={() => shareIssue(item)}
          class="bg-blue-500 text-white px-2 py-1 rounded hover:bg-blue-600 text-sm"
        >
          📤 Share
        </button>
        <button
          on:click={() => exportToCalendar(item)}
          class="bg-green-500 text-white px-2 py-1 rounded hover:bg-green-600 text-sm"
        >
          📅 Export
        </button>
        <button
          on:click={() => deleteIssue(item)}
          class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600 text-sm"
        >
          🗑 Delete
        </button>
      </div>
    </article>
  {/each}
</section>
