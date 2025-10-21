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
  class="p-4 bg-gradient-to-b from-pink-50 to-white rounded-2xl shadow-md 
             h-[550px] w-[320px] space-y-3 overflow-y-auto transition-all 
             duration-300 hover:shadow-xl border border-pink-200"
  on:dragover={dragOver}
  on:drop={onDrop}
>
  <h2 class="flex justify-between items-center text-lg font-semibold text-pink-700 border-b border-pink-200 pb-2">
    <span>{title}</span>
    <span class="text-pink-600 font-bold">SP: {storyPointsSum}</span>
  </h2>

  {#each items as item (item.id)}
    <article
      draggable="true"
      on:dragstart={(event) => onDragStart(item, event)}
      class="p-4 bg-gradient-to-br from-pink-100 to-pink-200 rounded-xl 
                 cursor-grab flex flex-col space-y-2 relative border border-pink-300 
                 hover:shadow-lg transition-all duration-200"
      animate:flip
    >
      {#if isOverdue(item.dueDate)}
        <div class="absolute top-0 left-0 h-full w-1 bg-red-500 rounded-r"></div>
      {/if}

      <div class="ml-1 space-y-1">
        <p class="text-lg font-bold text-gray-800">{item.title}</p>
        <p class="text-sm text-gray-700"><span class="font-semibold text-pink-700">Priority:</span> {item.priority}</p>
        <p class="text-sm text-gray-700"><span class="font-semibold text-pink-700">Due:</span> {formatDate(item.dueDate)}</p>
        <p class="text-sm text-gray-700"><span class="font-semibold text-pink-700">Created:</span> {formatDate(item.creationDate)}</p>
        <p class="text-sm text-gray-700"><span class="font-semibold text-pink-700">Story Points:</span> {item.storyPoints}</p>
      </div>

      <div class="flex flex-wrap gap-2 pt-2">
        <button
          on:click={() => shareIssue(item)}
          class="flex-1 bg-pink-500 text-white px-3 py-1.5 rounded-lg hover:bg-pink-600 text-sm font-medium transition"
        >
          📤 Share
        </button>
        <button
          on:click={() => exportToCalendar(item)}
          class="flex-1 bg-pink-500 text-white px-3 py-1.5 rounded-lg hover:bg-pink-600 text-sm font-medium transition"
        >
          📅 Export
        </button>
        <button
          on:click={() => deleteIssue(item)}
          class="flex-1 bg-pink-500 text-white px-3 py-1.5 rounded-lg hover:bg-pink-600 text-sm font-medium transition"
        >
          🗑 Delete
        </button>
      </div>
    </article>
  {/each}
</section>
