<script>
    import Lane from './Lane.svelte';
    import Assignment from './Assignment.svelte';
    import { onMount } from 'svelte';
  
    let assignmentRef;
  
    const STORAGE_KEY = 'kanban-board-state';
  
    // Prioritäten Reihenfolge für Sortierung
    const priorityOrder = { 'Critical': 1, 'High': 2, 'Medium': 3, 'Low': 4 };
  
    // Initial leer — wird aus localStorage geladen oder default gesetzt
    let Do = [];
    let Doing = [];
    let Done = [];
    let Archive = [];
  
    // Lädt gespeicherten Zustand
    onMount(() => {
      const saved = localStorage.getItem(STORAGE_KEY);
      if (saved) {
        const data = JSON.parse(saved);
        Do = data.Do || [];
        Doing = data.Doing || [];
        Done = data.Done || [];
        Archive = data.Archive || [];
      } else {
        // Default-Daten wenn noch nichts gespeichert ist
        Do = [];
        Doing = [];
        Done = [];
        Archive = [];
      }
    });
  
    // Speichert aktuellen Zustand in localStorage
    function saveState() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify({ Do, Doing, Done, Archive }));
    }
  
    // Aufgaben aus allen Lanes zusammen (Hilfsarray)
    $: allTasks = [...Do, ...Doing, ...Done, ...Archive];
  
    // Hilfsfunktion zum Verschieben von Tasks
    function moveItemToLane(itemId, targetLane) {
      // Task aus allen Lanes entfernen
      Do = Do.filter(i => i.id !== itemId);
      Doing = Doing.filter(i => i.id !== itemId);
      Done = Done.filter(i => i.id !== itemId);
      Archive = Archive.filter(i => i.id !== itemId);
  
      // Task finden (im vorherigen allTasks)
      let item = allTasks.find(i => i.id === itemId);
      if (!item) return;
  
      // Task zum Ziel-Lane hinzufügen
      if (targetLane === 'Do') Do = [...Do, item];
      else if (targetLane === 'Doing') Doing = [...Doing, item];
      else if (targetLane === 'Done') Done = [...Done, item];
      else if (targetLane === 'Archive') Archive = [...Archive, item];
  
      saveState(); // Zustand speichern
    }
  
    // Drag & Drop Funktionen
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
  
    // Neues Issue hinzufügen: immer in Do lane und sortieren
    function handleSubmit(event) {
      const newIssue = event.detail;
      Do = [...Do, newIssue];
      Do.sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
      saveState(); // Zustand speichern
    }
  
    function openAssignmentDialog() {
      assignmentRef.open();
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
  