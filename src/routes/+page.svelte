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
</script>

<Assignment bind:this={assignmentRef} on:submit={handleSubmit} />

<Board on:updateAssignments={handleUpdate} />
