<script>
    import Lane from './Lane.svelte';
    import Assignment from './Assignment.svelte';
    import { onMount } from 'svelte';
  
    let assignmentRef;
    const STORAGE_KEY = 'kanban-board-state';
  
    // Priority sorting order
    const priorityOrder = {
      'Critical': 1,
      'High': 2,
      'Medium': 3,
      'Low': 4
    };
  
    let Do = [];
    let Doing = [];
    let Done = [];
    let Archive = [];
  
    // Load saved state from localStorage on app start
    onMount(() => {
      const saved = localStorage.getItem(STORAGE_KEY);
      if (saved) {
        const data = JSON.parse(saved);
        Do = data.Do || [];
        Doing = data.Doing || [];
        Done = data.Done || [];
        Archive = data.Archive || [];
      } else {
        // Optional: start with dummy data
        Do = [];
        Doing = [];
        Done = [];
        Archive = [];
      }
    });
  
    function saveState() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify({ Do, Doing, Done, Archive }));
    }
  
    $: allTasks = [...Do, ...Doing, ...Done, ...Archive];
  
    // Move task to a lane and sort by priority
    function moveItemToLane(itemId, targetLane) {
      Do = Do.filter(i => i.id !== itemId);
      Doing = Doing.filter(i => i.id !== itemId);
      Done = Done.filter(i => i.id !== itemId);
      Archive = Archive.filter(i => i.id !== itemId);
  
      const item = allTasks.find(i => i.id === itemId);
      if (!item) return;
  
      if (targetLane === 'Do') {
        Do = [...Do, item].sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
      } else if (targetLane === 'Doing') {
        Doing = [...Doing, item].sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
      } else if (targetLane === 'Done') {
        Done = [...Done, item].sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
      } else if (targetLane === 'Archive') {
        Archive = [...Archive, item]; // Archive doesn't need sorting
      }
  
      saveState();
    }
  
    // Drag-and-drop functions
    function handleDragStart(item, event) {
      event.dataTransfer.setData("text/plain", item.id);
    }
  
    function dragOver(event) {
      event.preventDefault();
    }
  
    function dropToDo(event) {
      const itemId = parseInt(event.dataTransfer.getData("text/plain"), 10);
      moveItemToLane(itemId, 'Do');
    }
  
    function dropToDoing(event) {
      const itemId = parseInt(event.dataTransfer.getData("text/plain"), 10);
      moveItemToLane(itemId, 'Doing');
    }
  
    function dropToDone(event) {
      const itemId = parseInt(event.dataTransfer.getData("text/plain"), 10);
      moveItemToLane(itemId, 'Done');
    }
  
    function dropToArchive(event) {
      const itemId = parseInt(event.dataTransfer.getData("text/plain"), 10);
      moveItemToLane(itemId, 'Archive');
    }
  
    // Add new issue (from Assignment dialog)
    function handleSubmit(event) {
      const newIssue = event.detail;
      Do = [...Do, newIssue].sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
      saveState();
    }
  
    function openAssignmentDialog() {
      assignmentRef.open();
    }
  </script>
  
  <h1 class="text-center text-2xl font-bold mb-4">Project Kanban Board</h1>
  
  <div class="text-center mb-4">
    <button on:click={openAssignmentDialog} class="bg-pink-600 text-white px-4 py-2 rounded hover:bg-pink-700">
      ➕ Add New Issue
    </button>
  </div>
  
  <main class="p-8 w-full bg-pink-400 h-[700px] flex justify-between items-start">
    <Lane title="Do" items={Do} onDrop={dropToDo} onDragStart={handleDragStart} dragOver={dragOver} />
    <Lane title="Doing" items={Doing} onDrop={dropToDoing} onDragStart={handleDragStart} dragOver={dragOver} />
    <Lane title="Done" items={Done} onDrop={dropToDone} onDragStart={handleDragStart} dragOver={dragOver} />
    <Lane title="Archive" items={Archive} onDrop={dropToArchive} onDragStart={handleDragStart} dragOver={dragOver} />
  </main>
  
  <Assignment bind:this={assignmentRef} on:submit={handleSubmit} />
  