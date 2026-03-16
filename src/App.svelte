<script>
  // State
  let todos = [];
  let nextId = 1;
  let newText = '';

  // Edit state
  let editingId = null;
  let editText = '';

  // CRUD Operations

  function addTodo() {
    const trimmed = newText.trim();
    if (!trimmed) return;
    todos = [...todos, { id: nextId++, text: trimmed, completed: false }];
    newText = '';
  }

  function toggleTodo(id) {
    todos = todos.map(t => t.id === id ? { ...t, completed: !t.completed } : t);
  }

  function deleteTodo(id) {
    todos = todos.filter(t => t.id !== id);
    if (editingId === id) cancelEdit();
  }

  function startEdit(todo) {
    editingId = todo.id;
    editText = todo.text;
  }

  function saveEdit(id) {
    const trimmed = editText.trim();
    if (!trimmed) return;
    todos = todos.map(t => t.id === id ? { ...t, text: trimmed } : t);
    cancelEdit();
  }

  function cancelEdit() {
    editingId = null;
    editText = '';
  }

  function handleAddKey(e) {
    if (e.key === 'Enter') addTodo();
  }

  function handleEditKey(e, id) {
    if (e.key === 'Enter') saveEdit(id);
    if (e.key === 'Escape') cancelEdit();
  }

  $: remaining = todos.filter(t => !t.completed).length;
  $: total = todos.length;
</script>

<main>
  <h1>Todo List</h1>

  <div class="input-row">
    <input
      type="text"
      placeholder="What needs to be done?"
      bind:value={newText}
      on:keydown={handleAddKey}
    />
    <button class="btn-add" on:click={addTodo}>Add</button>
  </div>

  {#if total > 0}
    <p class="stats">{remaining} of {total} remaining</p>
  {/if}

  <ul>
    {#each todos as todo (todo.id)}
      <li class:completed={todo.completed}>
        {#if editingId === todo.id}
          <input
            class="edit-input"
            type="text"
            bind:value={editText}
            on:keydown={(e) => handleEditKey(e, todo.id)}
            autofocus
          />
          <div class="actions">
            <button class="btn-save" on:click={() => saveEdit(todo.id)}>Save</button>
            <button class="btn-cancel" on:click={cancelEdit}>Cancel</button>
          </div>
        {:else}
          <input
            type="checkbox"
            checked={todo.completed}
            on:change={() => toggleTodo(todo.id)}
          />
          <span class="todo-text">{todo.text}</span>
          <div class="actions">
            <button class="btn-edit" on:click={() => startEdit(todo)}>Edit</button>
            <button class="btn-delete" on:click={() => deleteTodo(todo.id)}>Delete</button>
          </div>
        {/if}
      </li>
    {:else}
      <p class="empty">No todos yet. Add one above!</p>
    {/each}
  </ul>
</main>

<style>
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }

  main {
    max-width: 540px;
    margin: 60px auto;
    padding: 0 16px;
    font-family: system-ui, sans-serif;
    color: #1a1a2e;
  }

  h1 {
    font-size: 2rem;
    font-weight: 700;
    margin-bottom: 24px;
    text-align: center;
  }

  .input-row {
    display: flex;
    gap: 8px;
    margin-bottom: 12px;
  }

  .input-row input {
    flex: 1;
    padding: 10px 14px;
    border: 2px solid #ddd;
    border-radius: 8px;
    font-size: 1rem;
    outline: none;
    transition: border-color 0.2s;
  }

  .input-row input:focus {
    border-color: #6c63ff;
  }

  .btn-add {
    padding: 10px 18px;
    background: #6c63ff;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 1rem;
    cursor: pointer;
    font-weight: 600;
    transition: background 0.2s;
  }

  .btn-add:hover {
    background: #574fd6;
  }

  .stats {
    font-size: 0.85rem;
    color: #888;
    margin-bottom: 16px;
    text-align: right;
  }

  ul {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  li {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px 14px;
    background: #f9f9f9;
    border: 1px solid #eee;
    border-radius: 10px;
  }

  li.completed .todo-text {
    text-decoration: line-through;
    color: #aaa;
  }

  li input[type="checkbox"] {
    width: 18px;
    height: 18px;
    cursor: pointer;
    accent-color: #6c63ff;
    flex-shrink: 0;
  }

  .todo-text {
    flex: 1;
    font-size: 1rem;
    word-break: break-word;
  }

  .edit-input {
    flex: 1;
    padding: 6px 10px;
    border: 2px solid #6c63ff;
    border-radius: 6px;
    font-size: 1rem;
    outline: none;
  }

  .actions {
    display: flex;
    gap: 6px;
    flex-shrink: 0;
  }

  button {
    padding: 6px 12px;
    border: none;
    border-radius: 6px;
    font-size: 0.85rem;
    cursor: pointer;
    font-weight: 500;
    transition: opacity 0.2s;
  }

  button:hover { opacity: 0.85; }

  .btn-edit   { background: #e0e7ff; color: #4338ca; }
  .btn-delete { background: #fee2e2; color: #dc2626; }
  .btn-save   { background: #d1fae5; color: #065f46; }
  .btn-cancel { background: #f3f4f6; color: #374151; }

  .empty {
    text-align: center;
    color: #aaa;
    font-size: 0.95rem;
    padding: 24px 0;
  }
</style>
