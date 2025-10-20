<script>
  import Board from '$lib/components/Board.svelte';
  import Assignment from '$lib/components/Assignment.svelte';

  let assignmentRef;
  let assignments = [];

  function openDialog() {
    assignmentRef.open();
  }

  function handleSubmit(event) {
    const newIssue = event.detail;
    assignments = [...assignments, newIssue];
  }

  function handleUpdate(event) {
    assignments = event.detail;
  }

  function exportToCSV() {
    if (!assignments || assignments.length === 0) {
      alert('No items to export.');
      return;
    }

    const headers = ['Title', 'Description', 'Creation Date', 'Due Date', 'Story Points', 'Priority'];
    const rows = assignments.map(a => [
      a.title,
      a.description,
      a.creationDate,
      a.dueDate,
      a.storyPoints,
      a.priority
    ]);

    const csvContent = [headers, ...rows]
      .map(row => row.map(v => `"${String(v || '').replace(/"/g, '""')}"`).join(','))
      .join('\n');

    const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
    const url = URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = 'kanban_items.csv';
    link.click();
    URL.revokeObjectURL(url);
  }
</script>

<Assignment bind:this={assignmentRef} on:submit={handleSubmit} />

<div class="flex justify-end mt-4">
  <button
    on:click={exportToCSV}
    class="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition"
  >
    📤 Export as CSV
  </button>
</div>

<Board on:updateAssignments={handleUpdate} />
