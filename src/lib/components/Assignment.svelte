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
      id: Date.now(),
      title,
      description,
      creationDate,
      dueDate,
      storyPoints,
      priority
    };
    dispatch('submit', issue);
    close();

    // Reset form
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
  class="rounded-2xl p-8 max-w-lg w-[90vw] bg-gradient-to-br from-pink-50 to-pink-100 shadow-2xl text-gray-800 font-sans backdrop:bg-black/40 backdrop:backdrop-blur-sm border border-pink-200 animate-fadeIn"
  aria-labelledby="new-issue-title"
>
  <form
    method="dialog"
    on:submit|preventDefault={submitIssue}
    class="space-y-6"
  >
    <h2
      id="new-issue-title"
      class="text-2xl font-bold text-center text-pink-700 border-b border-pink-200 pb-3"
    >
      Neues Issue anlegen
    </h2>

    <div class="space-y-4">
      <label class="block font-semibold text-sm">
        Title:
        <input
          type="text"
          bind:value={title}
          required
          placeholder="Enter issue title..."
          class="mt-1 block w-full rounded-xl border border-gray-300 px-4 py-2.5 bg-white focus:outline-none focus:ring-2 focus:ring-pink-400 shadow-sm transition"
        />
      </label>

      <label class="block font-semibold text-sm">
        Description:
        <textarea
          bind:value={description}
          required
          placeholder="Briefly describe the issue..."
          class="mt-1 block w-full rounded-xl border border-gray-300 px-4 py-2.5 bg-white resize-y min-h-[90px] focus:outline-none focus:ring-2 focus:ring-pink-400 shadow-sm transition"
        ></textarea>
      </label>

      <div class="grid grid-cols-2 gap-4">
        <label class="block font-semibold text-sm">
          Creation Date:
          <input
            type="date"
            bind:value={creationDate}
            required
            class="mt-1 block w-full rounded-xl border border-gray-300 px-3 py-2 bg-white focus:outline-none focus:ring-2 focus:ring-pink-400 shadow-sm transition"
          />
        </label>

        <label class="block font-semibold text-sm">
          Due Date:
          <input
            type="date"
            bind:value={dueDate}
            required
            class="mt-1 block w-full rounded-xl border border-gray-300 px-3 py-2 bg-white focus:outline-none focus:ring-2 focus:ring-pink-400 shadow-sm transition"
          />
        </label>
      </div>

      <div class="grid grid-cols-2 gap-4">
        <label class="block font-semibold text-sm">
          Story Points:
          <input
            type="number"
            min="0"
            bind:value={storyPoints}
            required
            class="mt-1 block w-full rounded-xl border border-gray-300 px-3 py-2 bg-white focus:outline-none focus:ring-2 focus:ring-pink-400 shadow-sm transition"
          />
        </label>

        <label class="block font-semibold text-sm">
          Priority:
          <select
            bind:value={priority}
            class="mt-1 block w-full rounded-xl border border-gray-300 px-3 py-2 bg-white focus:outline-none focus:ring-2 focus:ring-pink-400 shadow-sm transition"
          >
            <option>Low</option>
            <option>Medium</option>
            <option>High</option>
            <option>Critical</option>
          </select>
        </label>
      </div>
    </div>

    <div class="flex justify-end gap-3 pt-6 border-t border-pink-200">
      <button
        type="button"
        on:click={close}
        class="rounded-xl px-5 py-2.5 bg-gray-200 hover:bg-gray-300 text-gray-700 font-semibold transition shadow-sm"
      >
        Cancel
      </button>
      <button
        type="submit"
        class="rounded-xl px-5 py-2.5 bg-pink-600 hover:bg-pink-700 text-white font-semibold transition shadow-sm"
      >
        Create
      </button>
    </div>
  </form>
</dialog>

<style>
  @keyframes fadeIn {
    from {
      opacity: 0;
      transform: scale(0.95);
    }
    to {
      opacity: 1;
      transform: scale(1);
    }
  }

  .animate-fadeIn {
    animation: fadeIn 0.2s ease-in-out;
  }
</style>
