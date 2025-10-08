<script>
    import Lane from './Lane.svelte';
  
    let Do = [1, 2];
    let Doing = [3, 4];
    let Done = [5, 6];
    let Archive = [7, 8];
  
    function handleDragStart(item, event) {
      event.dataTransfer.setData("text/plain", item);
    }
  
    function dragOver(event) {
      event.preventDefault();
    }
  
    function dropToDo(event) {
      let item = parseInt(event.dataTransfer.getData("text/plain"), 10);
      Doing = Doing.filter(i => i !== item);
      Done = Done.filter(i => i !== item);
      Archive = Archive.filter(i => i !== item);
      Do = [...Do, item];
    }
  
    function dropToDoing(event) {
      let item = parseInt(event.dataTransfer.getData("text/plain"), 10);
      Do = Do.filter(i => i !== item);
      Done = Done.filter(i => i !== item);
      Archive = Archive.filter(i => i !== item);
      Doing = [...Doing, item];
    }
  
    function dropToDone(event) {
      let item = parseInt(event.dataTransfer.getData("text/plain"), 10);
      Do = Do.filter(i => i !== item);
      Doing = Doing.filter(i => i !== item);
      Archive = Archive.filter(i => i !== item);
      Done = [...Done, item];
    }
  
    function dropToArchive(event) {
      let item = parseInt(event.dataTransfer.getData("text/plain"), 10);
      Do = Do.filter(i => i !== item);
      Doing = Doing.filter(i => i !== item);
      Done = Done.filter(i => i !== item);
      Archive = [...Archive, item];
    }
  </script>
  
  <h1 class="text-center">Project Kanban Board</h1>
  
  <main class="p-32 w-full bg-pink-400 h-[700px] flex justify-between items-center">
    <Lane title="Do" items={Do} onDrop={dropToDo} onDragStart={handleDragStart} dragOver={dragOver} />
    <Lane title="Doing" items={Doing} onDrop={dropToDoing} onDragStart={handleDragStart} dragOver={dragOver} />
    <Lane title="Done" items={Done} onDrop={dropToDone} onDragStart={handleDragStart} dragOver={dragOver} />
    <Lane title="Archive" items={Archive} onDrop={dropToArchive} onDragStart={handleDragStart} dragOver={dragOver} />
  </main>
  