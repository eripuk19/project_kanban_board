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
  
  <dialog
  bind:this={dialog}
  class="rounded-xl p-8 max-w-lg w-[90vw] shadow-lg bg-pink-100 text-gray-800 font-sans animate-fadeIn"
  aria-labelledby="new-issue-title"
>
  <form method="dialog" on:submit|preventDefault={submitIssue} class="space-y-6">
    <h2 id="new-issue-title" class="text-2xl font-bold text-pink-700 mb-4">
      Neues Issue anlegen
    </h2>

    <label class="block font-semibold">
      Title:
      <input
        type="text"
        bind:value={title}
        required
        class="mt-1 block w-full rounded-lg border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-pink-400"
      />
    </label>

    <label class="block font-semibold">
      Description:
      <textarea
        bind:value={description}
        required
        class="mt-1 block w-full rounded-lg border border-gray-300 px-3 py-2 resize-y min-h-[80px] focus:outline-none focus:ring-2 focus:ring-pink-400"
      ></textarea>
    </label>

    <label class="block font-semibold">
      Creation Date:
      <input
        type="date"
        bind:value={creationDate}
        required
        class="mt-1 block w-full rounded-lg border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-pink-400"
      />
    </label>

    <label class="block font-semibold">
      Due Date:
      <input
        type="date"
        bind:value={dueDate}
        required
        class="mt-1 block w-full rounded-lg border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-pink-400"
      />
    </label>

    <label class="block font-semibold">
      Story Points:
      <input
        type="number"
        min="0"
        bind:value={storyPoints}
        required
        class="mt-1 block w-full rounded-lg border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-pink-400"
      />
    </label>

    <label class="block font-semibold">
      Priority:
      <select
        bind:value={priority}
        class="mt-1 block w-full rounded-lg border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-pink-400"
      >
        <option>Low</option>
        <option>Medium</option>
        <option>High</option>
        <option>Critical</option>
      </select>
    </label>

    <div class="flex justify-end space-x-3 pt-4">
      <button
        type="button"
        on:click={close}
        class="rounded-lg px-4 py-2 bg-gray-200 hover:bg-gray-300 text-gray-700 font-semibold transition"
      >
        Cancel
      </button>
      <button
        type="submit"
        class="rounded-lg px-4 py-2 bg-pink-600 hover:bg-pink-700 text-white font-semibold transition"
      >
        Create
      </button>
    </div>
  </form>
</dialog>
