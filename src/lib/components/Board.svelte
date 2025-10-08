<script>
    import Lane from './Lane.svelte';
    import Assignment from './Assignment.svelte';
  
    let assignmentRef;
  
    // Initial tasks for each lane
    let Do = [];
    let Doing = [];
    let Done = [];
    let Archive = [];
  
    function handleDragStart(item, event) {
      event.dataTransfer.setData("text/plain", item.id);
    }
  
    function dragOver(event) {
      event.preventDefault();
    }
  
    function dropToDo(event) {
      let itemId = parseInt(event.dataTransfer.getData("text/plain"), 10);
      moveItemToLane(itemId, 'Do');
    }
  
    function dropToDoing(event) {
      let itemId = parseInt(event.dataTransfer.getData("text/plain"), 10);
      moveItemToLane(itemId, 'Doing');
    }
  
    function dropToDone(event) {
      let itemId = parseInt(event.dataTransfer.getData("text/plain"), 10);
      moveItemToLane(itemId, 'Done');
    }
  
    function dropToArchive(event) {
      let itemId = parseInt(event.dataTransfer.getData("text/plain"), 10);
      moveItemToLane(itemId, 'Archive');
    }
  
    // Helper function to find and move tasks between lanes
    function moveItemToLane(itemId, targetLane) {
      // Remove from all lanes
      Do = Do.filter(i => i.id !== itemId);
      Doing = Doing.filter(i => i.id !== itemId);
      Done = Done.filter(i => i.id !== itemId);
      Archive = Archive.filter(i => i.id !== itemId);
  
      // Find the task object (search from all lanes including removed)
      let item = allTasks.find(i => i.id === itemId);
  
      if (!item) return;
  
      // Add to target lane
      if (targetLane === 'Do') Do = [...Do, item];
      else if (targetLane === 'Doing') Doing = [...Doing, item];
      else if (targetLane === 'Done') Done = [...Done, item];
      else if (targetLane === 'Archive') Archive = [...Archive, item];
    }
  
    // We maintain a combined array to help searching for tasks
    $: allTasks = [...Do, ...Doing, ...Done, ...Archive];
  
    // Open the assignment dialog
    function openAssignmentDialog() {
      assignmentRef.open();
    }
  
    // When a new issue is created, add it to Do lane and sort Do lane by priority
    const priorityOrder = { 'Critical': 1, 'High': 2, 'Medium': 3, 'Low': 4 };
  
    function handleSubmit(event) {
      const newIssue = event.detail;
      Do = [...Do, newIssue];
  
      // Sort Do by priority order
      Do.sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
    }
  </script>
  
  <h1 class="text-center">Project Kanban Board</h1>
  
  <button on:click={openAssignmentDialog} style="margin-bottom: 20px;">Add New Issue</button>
  
  <main class="p-8 w-full bg-pink-400 h-[700px] flex justify-between items-start">
    <Lane title="Do" items={Do} onDrop={dropToDo} onDragStart={handleDragStart} dragOver={dragOver} />
    <Lane title="Doing" items={Doing} onDrop={dropToDoing} onDragStart={handleDragStart} dragOver={dragOver} />
    <Lane title="Done" items={Done} onDrop={dropToDone} onDragStart={handleDragStart} dragOver={dragOver} />
    <Lane title="Archive" items={Archive} onDrop={dropToArchive} onDragStart={handleDragStart} dragOver={dragOver} />
  </main>
  
  <Assignment bind:this={assignmentRef} on:submit={handleSubmit} />
  