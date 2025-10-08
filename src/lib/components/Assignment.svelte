<script>
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();
  
    let dialog;
    let title = '';
    let description = '';
    let creationDate = '';
    let dueDate = '';
    let storyPoints = '';
    let priority = 'Medium';
  
    export function open() {
      dialog.showModal();
    }
  
    function close() {
      dialog.close();
    }
  
    function submitIssue() {
      const issue = {
        id: Date.now(),  // unique id
        title,
        description,
        creationDate,
        dueDate,
        storyPoints,
        priority
      };
      dispatch('submit', issue);
      close();
  
      // Reset form fields
      title = '';
      description = '';
      creationDate = '';
      dueDate = '';
      storyPoints = '';
      priority = 'Medium';
    }
  </script>
  
  <dialog bind:this={dialog}>
    <form method="dialog" on:submit|preventDefault={submitIssue}>
      <h2>Neues Issue anlegen</h2>
  
      <label>Title:<br />
        <input type="text" bind:value={title} required />
      </label><br />
  
      <label>Description:<br />
        <textarea bind:value={description} required></textarea>
      </label><br />
  
      <label>Creation Date:<br />
        <input type="date" bind:value={creationDate} required />
      </label><br />
  
      <label>Due Date:<br />
        <input type="date" bind:value={dueDate} required />
      </label><br />
  
      <label>Story Points:<br />
        <input type="number" min="0" bind:value={storyPoints} required />
      </label><br />
  
      <label>Priority:<br />
        <select bind:value={priority}>
          <option>Low</option>
          <option>Medium</option>
          <option>High</option>
          <option>Critical</option>
        </select>
      </label><br />
  
      <button type="button" on:click={close}>Cancel</button>
      <button type="submit">Create</button>
    </form>
  </dialog>
  