<script>
  import Board from '$lib/components/Board.svelte';


  import Assignment from '$lib/components/Assignment.svelte';

  let assignmentRef;

  // Priority order for sorting
  const priorityOrder = {
    'Critical': 1,
    'High': 2,
    'Medium': 3,
    'Low': 4
  };

  let assignments = [];

  function openDialog() {
    assignmentRef.open();
  }

  function handleSubmit(event) {
    const newIssue = event.detail;

    assignments = [...assignments, newIssue];

    // Sort assignments by priority
    assignments.sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
  }
</script>


<Assignment bind:this={assignmentRef} on:submit={handleSubmit} />

<table class="border border-collapse border-gray-300 mt-5 w-full">
  
  <tbody>
    {#each assignments as issue}
      <tr>
        <td>{issue.title}</td>
        <td>{issue.description}</td>
        <td>{issue.creationDate}</td>
        <td>{issue.dueDate}</td>
        <td>{issue.storyPoints}</td>
        <td>{issue.priority}</td>
      </tr>
    {/each}
  </tbody>

  <Board></Board>

</table>
