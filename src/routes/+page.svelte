<script>
    import { flip } from 'svelte/animate';
  
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
  
  <main class="p-32 w-full bg-gray-400 h-[700px] flex justify-between items-center">
    <section class="p-3 bg-white h-[400px] w-[300px] space-y-2" ondragover={dragOver} ondrop={dropToDo}>
      <h2>Do</h2>
      {#each Do as item (item)}
        <article ondragstart={(event) => handleDragStart(item, event)} class="p-4 bg-blue-200 cursor-grab" draggable="true" animate:flip>
          {item}
        </article>
      {/each}
    </section>
  
    <section class="p-3 bg-white h-[400px] w-[300px] space-y-2" ondragover={dragOver} ondrop={dropToDoing}>
      <h2>Doing</h2>
      {#each Doing as item (item)}
        <article ondragstart={(event) => handleDragStart(item, event)} class="p-4 bg-blue-200 cursor-grab" draggable="true" animate:flip>
          {item}
        </article>
      {/each}
    </section>
  
    <section class="p-3 bg-white h-[400px] w-[300px] space-y-2" ondragover={dragOver} ondrop={dropToDone}>
      <h2>Done</h2>
      {#each Done as item (item)}
        <article ondragstart={(event) => handleDragStart(item, event)} class="p-4 bg-blue-200 cursor-grab" draggable="true" animate:flip >
          {item}
        </article>
      {/each}
    </section>
  
    <section class="p-3 bg-white h-[400px] w-[300px] space-y-2" ondragover={dragOver} ondrop={dropToArchive}>
      <h2>Archive</h2>
      {#each Archive as item (item)}
        <article ondragstart={(event) => handleDragStart(item, event)} class="p-4 bg-blue-200 cursor-grab" draggable="true" animate:flip >
          {item}
        </article>
      {/each}
    </section>
  </main>
  