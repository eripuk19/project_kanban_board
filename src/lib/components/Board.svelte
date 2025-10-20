<script>
  import Lane from './Lane.svelte';
  import Assignment from './Assignment.svelte';
  import { onMount, createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();
  let assignmentRef;
  const STORAGE_KEY = 'kanban-board-state';

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

  // --- Country detection ---
  let country = 'Loading...';

  async function fetchCountry() {
    try {
      const res = await fetch('https://ipapi.co/json/');
      const data = await res.json();
      country = data.country_name || 'Unknown';
    } catch (err) {
      console.error('Error fetching country:', err);
      country = 'Unknown';
    }
  }

  onMount(() => {
    // Load saved state
    const saved = localStorage.getItem(STORAGE_KEY);
    if (saved) {
      const data = JSON.parse(saved);
      Do = data.Do || [];
      Doing = data.Doing || [];
      Done = data.Done || [];
      Archive = data.Archive || [];
    }

    // Notification permission
    if ("Notification" in window && Notification.permission !== "granted") {
      Notification.requestPermission();
    }

    dispatchUpdatedAssignments();
    fetchCountry(); // fetch country on mount
  });

  function saveState() {
    localStorage.setItem(STORAGE_KEY, JSON.stringify({ Do, Doing, Done, Archive }));
    dispatchUpdatedAssignments();
  }

  $: allTasks = [...Do, ...Doing, ...Done, ...Archive];
  $: sumDo = Do.reduce((acc, task) => acc + Number(task.storyPoints || 0), 0);
  $: sumDoing = Doing.reduce((acc, task) => acc + Number(task.storyPoints || 0), 0);
  $: sumDone = Done.reduce((acc, task) => acc + Number(task.storyPoints || 0), 0);
  $: sumArchive = Archive.reduce((acc, task) => acc + Number(task.storyPoints || 0), 0);

  function showNativeNotification(title, body) {
    if ("Notification" in window && Notification.permission === "granted") {
      new Notification(title, { body });
    }
  }

  function moveItemToLane(itemId, targetLane) {
    Do = Do.filter(i => i.id !== itemId);
    Doing = Doing.filter(i => i.id !== itemId);
    Done = Done.filter(i => i.id !== itemId);
    Archive = Archive.filter(i => i.id !== itemId);

    const item = allTasks.find(i => i.id === itemId);
    if (!item) return;

    if (targetLane === 'Do') Do = [...Do, item].sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
    else if (targetLane === 'Doing') Doing = [...Doing, item].sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
    else if (targetLane === 'Done') {
      Done = [...Done, item].sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
      showNativeNotification("Task Completed ✅", `Task "${item.title}" was moved to Done.`);
    }
    else if (targetLane === 'Archive') Archive = [...Archive, item];

    saveState();
  }

  function dispatchUpdatedAssignments() {
    const allIssues = [...Do, ...Doing, ...Done, ...Archive];
    dispatch('updateAssignments', allIssues);
  }

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

  function handleSubmit(event) {
    const newIssue = event.detail;
    Do = [...Do, newIssue].sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
    saveState();
  }

  function openAssignmentDialog() {
    assignmentRef.open();
  }

  function handleDelete(itemId) {
    Do = Do.filter(i => i.id !== itemId);
    Doing = Doing.filter(i => i.id !== itemId);
    Done = Done.filter(i => i.id !== itemId);
    Archive = Archive.filter(i => i.id !== itemId);
    saveState();
  }
</script>

<h1 class="text-center text-2xl font-bold mb-4">Project Kanban Board</h1>

<div class="text-center mb-4">
  <button on:click={openAssignmentDialog} class="bg-pink-600 text-white px-4 py-2 rounded hover:bg-pink-700">
    ➕ Add New Issue
  </button>
</div>

<main class="p-8 w-full bg-pink-400 h-[700px] flex justify-between items-start">
  <Lane
    title="Do"
    items={Do}
    onDrop={dropToDo}
    onDragStart={handleDragStart}
    dragOver={dragOver}
    storyPointsSum={sumDo}
    on:deleteIssue={(e) => handleDelete(e.detail)}
  />
  <Lane
    title="Doing"
    items={Doing}
    onDrop={dropToDoing}
    onDragStart={handleDragStart}
    dragOver={dragOver}
    storyPointsSum={sumDoing}
    on:deleteIssue={(e) => handleDelete(e.detail)}
  />
  <Lane
    title="Done"
    items={Done}
    onDrop={dropToDone}
    onDragStart={handleDragStart}
    dragOver={dragOver}
    storyPointsSum={sumDone}
    on:deleteIssue={(e) => handleDelete(e.detail)}
  />
  <Lane
    title="Archive"
    items={Archive}
    onDrop={dropToArchive}
    onDragStart={handleDragStart}
    dragOver={dragOver}
    storyPointsSum={sumArchive}
    on:deleteIssue={(e) => handleDelete(e.detail)}
  />
</main>

<Assignment bind:this={assignmentRef} on:submit={handleSubmit} />

<footer class="w-full bg-pink-600 text-white text-center py-4 mt-4 rounded-t-lg shadow-lg">
  <p class="text-lg font-semibold">
    🌍 Your country: {country}
  </p>
</footer>
