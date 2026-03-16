<script>
  // Theme State
  let dark = false;

  // Multiple Lists State
  let lists = [{ id: 1, name: 'My List', todos: [] }];
  let activeListId = 1;
  let nextListId = 2;
  let newListName = '';

  // Todo State
  let nextId = 1;
  let newText = '';

  // Edit state
  let editingId = null;
  let editText = '';

  // Derived state
  $: activeList = lists.find(l => l.id === activeListId);
  $: remaining = activeList.todos.filter(t => !t.completed).length;
  $: total = activeList.todos.length;

  // CRUD Operations for Todos

  function addTodo() {
    const trimmed = newText.trim();
    if (!trimmed) return;
    activeList.todos = [...activeList.todos, { id: nextId++, text: trimmed, completed: false }];
    lists = [...lists];
    newText = '';
    if (initialized) saveToStorage();
  }

  function toggleTodo(id) {
    activeList.todos = activeList.todos.map(t => t.id === id ? { ...t, completed: !t.completed } : t);
    lists = [...lists];
    if (initialized) saveToStorage();
  }

  function deleteTodo(id) {
    activeList.todos = activeList.todos.filter(t => t.id !== id);
    lists = [...lists];
    if (editingId === id) cancelEdit();
    if (initialized) saveToStorage();
  }

  function startEdit(todo) {
    editingId = todo.id;
    editText = todo.text;
  }

  function saveEdit(id) {
    const trimmed = editText.trim();
    if (!trimmed) return;
    activeList.todos = activeList.todos.map(t => t.id === id ? { ...t, text: trimmed } : t);
    lists = [...lists];
    cancelEdit();
    if (initialized) saveToStorage();
  }

  function cancelEdit() {
    editingId = null;
    editText = '';
  }

  // Multiple Lists Operations

  function addList() {
    const trimmed = newListName.trim();
    if (!trimmed) return;
    lists = [...lists, { id: nextListId++, name: trimmed, todos: [] }];
    activeListId = lists[lists.length - 1].id;
    newListName = '';
    if (initialized) saveToStorage();
  }

  function deleteList(id) {
    if (lists.length === 1) return; // Always keep at least 1 list
    lists = lists.filter(l => l.id !== id);
    if (activeListId === id) {
      activeListId = lists[0].id;
    }
    if (initialized) saveToStorage();
  }

  // Key Handlers

  function handleAddKey(e) {
    if (e.key === 'Enter') addTodo();
  }

  function handleEditKey(e, id) {
    if (e.key === 'Enter') saveEdit(id);
    if (e.key === 'Escape') cancelEdit();
  }

  function handleAddListKey(e) {
    if (e.key === 'Enter') addList();
  }

  // Mobile sidebar toggle
  let sidebarOpen = false;

  function toggleSidebar() {
    sidebarOpen = !sidebarOpen;
  }

  function closeSidebar() {
    sidebarOpen = false;
  }

  // Filter State
  let filter = 'all';

  // List rename editing
  let editingListId = null;
  let editListName = '';

  // Derived state for filtering
  $: visibleTodos = filter === 'all'
    ? activeList.todos
    : filter === 'active'
    ? activeList.todos.filter(t => !t.completed)
    : activeList.todos.filter(t => t.completed);

  $: completedCount = activeList.todos.filter(t => t.completed).length;

  // LocalStorage Persistence
  import { onMount } from 'svelte';
  let initialized = false;

  function loadFromStorage() {
    try {
      const saved = localStorage.getItem('todoo-data');
      if (saved) {
        const data = JSON.parse(saved);
        if (data.lists?.length > 0) {
          lists = data.lists;
          nextListId = data.nextListId || 2;
          nextId = data.nextId || 1;
          activeListId = data.activeListId || 1;
          dark = data.dark ?? false;
        }
      }
    } catch (e) {
      console.error('Failed to load from localStorage:', e);
    }
    initialized = true;
  }

  function saveToStorage() {
    if (!initialized) return; // Don't save until after load
    try {
      localStorage.setItem('todoo-data', JSON.stringify({
        lists,
        activeListId,
        nextListId,
        nextId,
        dark
      }));
    } catch (e) {
      console.error('Failed to save to localStorage:', e);
    }
  }

  onMount(() => {
    loadFromStorage();
  });

  // List Rename Functions
  function startEditList(id, name) {
    editingListId = id;
    editListName = name;
  }

  function saveListName(id) {
    const trimmed = editListName.trim();
    if (!trimmed) {
      cancelEditList();
      return;
    }
    lists = lists.map(l => l.id === id ? { ...l, name: trimmed } : l);
    cancelEditList();
    if (initialized) saveToStorage();
  }

  function cancelEditList() {
    editingListId = null;
    editListName = '';
  }

  function handleListNameKey(e, id) {
    if (e.key === 'Enter') saveListName(id);
    if (e.key === 'Escape') cancelEditList();
  }

  // Clear Completed Todos
  function clearCompleted() {
    activeList.todos = activeList.todos.filter(t => !t.completed);
    lists = [...lists];
    if (initialized) saveToStorage();
  }

  // Reset filter when switching lists
  $: activeListId, filter = 'all';

  // Save dark mode changes
  function toggleDarkMode() {
    dark = !dark;
    if (initialized) saveToStorage();
  }
</script>

<main class:dark>
  <div class="layout">
    <!-- Mobile Overlay -->
    {#if sidebarOpen}
      <button
        class="mobile-overlay"
        on:click={closeSidebar}
        type="button"
        aria-label="Close menu"
      ></button>
    {/if}

    <!-- Sidebar -->
    <aside class="sidebar" class:open={sidebarOpen}>
      <div class="lists-header">
        <h2>Lists</h2>
        <div class="header-actions">
          <button class="btn-theme" on:click={toggleDarkMode}>
            {dark ? '☀' : '☾'}
          </button>
          <button class="btn-close-sidebar" on:click={closeSidebar} title="Close">
            ✕
          </button>
        </div>
      </div>

      <ul class="lists">
        {#each lists as list (list.id)}
          <li class:active={activeListId === list.id}>
            {#if editingListId === list.id}
              <input
                class="edit-list-name"
                type="text"
                bind:value={editListName}
                on:keydown={(e) => handleListNameKey(e, list.id)}
                on:blur={() => saveListName(list.id)}
                autofocus
              />
            {:else}
              <button
                class="list-item"
                on:click={() => (activeListId = list.id)}
                on:dblclick={() => startEditList(list.id, list.name)}
              >
                {list.name}
              </button>
            {/if}
            {#if lists.length > 1}
              <button
                class="btn-delete-list"
                on:click={() => deleteList(list.id)}
                title="Delete list"
              >
                ×
              </button>
            {/if}
          </li>
        {/each}
      </ul>

      <div class="new-list-row">
        <input
          type="text"
          placeholder="New list..."
          bind:value={newListName}
          on:keydown={handleAddListKey}
        />
        <button class="btn-add-list" on:click={addList}>+</button>
      </div>
    </aside>

    <!-- Main Content -->
    <div class="content">
      <div class="mobile-header">
        <button class="btn-menu" on:click={toggleSidebar} title="Menu">
          ☰
        </button>
        <h1>{activeList.name}</h1>
      </div>

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
        <div class="stats-row">
          <p class="stats">{remaining} of {total} remaining</p>
          {#if completedCount > 0}
            <button class="btn-clear-completed" on:click={clearCompleted}>
              Clear completed
            </button>
          {/if}
        </div>

        <div class="filter-tabs">
          <button
            class="filter-tab"
            class:active={filter === 'all'}
            on:click={() => (filter = 'all')}
          >
            All
          </button>
          <button
            class="filter-tab"
            class:active={filter === 'active'}
            on:click={() => (filter = 'active')}
          >
            Active
          </button>
          <button
            class="filter-tab"
            class:active={filter === 'completed'}
            on:click={() => (filter = 'completed')}
          >
            Completed
          </button>
        </div>
      {/if}

      <ul class="todos">
        {#each visibleTodos as todo (todo.id)}
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
    </div>
  </div>
</main>

<style>
  :root {
    /* Light theme (defaults) */
    --bg: #ffffff;
    --surface: #f9f9f9;
    --border: #eee;
    --text: #1a1a2e;
    --text-muted: #888;
    --sidebar-bg: #f3f4f6;
    --sidebar-active: #e0e7ff;
    --sidebar-active-text: #4338ca;
    --accent: #6c63ff;
    --accent-dark: #574fd6;
  }

  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    border: none;
    outline: none;
  }

  :global(html),
  :global(body) {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
    overflow: hidden;
    background: #ffffff;
  }

  main {
    width: 100%;
    height: 100vh;
    background: var(--bg);
    color: var(--text);
    font-family: system-ui, sans-serif;
    transition: background 0.3s, color 0.3s;
    overflow-y: auto;
  }

  main.dark {
    --bg: #1a1a2e;
    --surface: #252540;
    --border: #333355;
    --text: #e2e2f0;
    --text-muted: #9999bb;
    --sidebar-bg: #13131f;
    --sidebar-active: #2d2b55;
    --sidebar-active-text: #a5b4fc;
    background: #1a1a2e;
  }

  .layout {
    display: flex;
    min-height: 100vh;
    max-width: 1200px;
    margin: 0 auto;
    width: 100%;
  }

  /* Mobile Overlay */
  .mobile-overlay {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.5);
    z-index: 99;
  }

  /* Sidebar */
  .sidebar {
    width: 220px;
    background: var(--sidebar-bg);
    border-right: 1px solid var(--border);
    padding: 20px 16px;
    display: flex;
    flex-direction: column;
    gap: 20px;
    transition: background 0.3s, border-color 0.3s;
    flex-shrink: 0;
  }

  .btn-close-sidebar {
    display: none;
    width: 32px;
    height: 32px;
    padding: 0;
    background: transparent;
    color: var(--text-muted);
    border: none;
    border-radius: 4px;
    font-size: 1rem;
    cursor: pointer;
    flex-shrink: 0;
    align-items: center;
    justify-content: center;
    transition: color 0.2s, background 0.2s;
  }

  .btn-close-sidebar:hover {
    color: var(--text);
    background: var(--border);
  }

  .lists-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .lists-header h2 {
    font-size: 1.1rem;
    font-weight: 600;
  }

  .header-actions {
    display: flex;
    gap: 8px;
    align-items: center;
  }

  .btn-theme {
    width: 36px;
    height: 36px;
    border: none;
    background: var(--surface);
    color: var(--text);
    border-radius: 6px;
    font-size: 1.2rem;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background 0.2s;
  }

  .btn-theme:hover {
    background: var(--border);
  }

  .lists {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 8px;
    flex: 1;
  }

  .lists li {
    display: flex;
    gap: 8px;
    align-items: center;
  }

  .list-item {
    flex: 1;
    padding: 10px 12px;
    background: transparent;
    color: var(--text);
    border: none;
    border-radius: 6px;
    font-size: 0.95rem;
    cursor: pointer;
    text-align: left;
    transition: background 0.2s;
  }

  .list-item:hover {
    background: var(--border);
  }

  .lists li.active .list-item {
    background: var(--sidebar-active);
    color: var(--sidebar-active-text);
    font-weight: 600;
  }

  .edit-list-name {
    flex: 1;
    padding: 8px 10px;
    background: var(--surface);
    color: var(--text);
    border: 2px solid var(--accent);
    border-radius: 4px;
    font-size: 0.95rem;
    font-weight: 500;
    outline: none;
  }

  .btn-delete-list {
    width: 28px;
    height: 28px;
    padding: 0;
    background: transparent;
    color: var(--text-muted);
    border: none;
    border-radius: 4px;
    font-size: 1.2rem;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-shrink: 0;
    transition: color 0.2s, background 0.2s;
  }

  .btn-delete-list:hover {
    color: var(--text);
    background: var(--border);
  }

  .new-list-row {
    display: flex;
    gap: 8px;
    flex-shrink: 0;
    margin-top: auto;
  }

  .new-list-row input {
    flex: 1;
    min-width: 0;
    padding: 8px 12px;
    background: var(--surface);
    color: var(--text);
    border: 1px solid var(--border);
    border-radius: 6px;
    font-size: 0.9rem;
    outline: none;
    transition: border-color 0.2s, background 0.2s;
  }

  .new-list-row input:focus {
    border-color: var(--accent);
  }

  .btn-add-list {
    width: 32px;
    height: 32px;
    padding: 0;
    background: var(--accent);
    color: white;
    border: none;
    border-radius: 6px;
    font-size: 1rem;
    cursor: pointer;
    font-weight: 600;
    transition: background 0.2s;
    flex-shrink: 0;
  }

  .btn-add-list:hover {
    background: var(--accent-dark);
  }

  /* Main Content */
  .content {
    flex: 1;
    width: 100%;
    padding: 40px 20px;
  }

  .mobile-header {
    display: none;
    flex-direction: row;
    align-items: center;
    gap: 16px;
    margin-bottom: 20px;
  }

  .btn-menu {
    display: none;
    width: 40px;
    height: 40px;
    padding: 0;
    background: var(--surface);
    color: var(--text);
    border: 1px solid var(--border);
    border-radius: 6px;
    font-size: 1.4rem;
    cursor: pointer;
    flex-shrink: 0;
    align-items: center;
    justify-content: center;
    transition: background 0.2s, border-color 0.2s;
  }

  .btn-menu:hover {
    background: var(--border);
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
    background: var(--surface);
    color: var(--text);
    border: 2px solid var(--border);
    border-radius: 8px;
    font-size: 1rem;
    outline: none;
    transition: border-color 0.2s, background 0.2s;
  }

  .input-row input:focus {
    border-color: var(--accent);
  }

  .btn-add {
    padding: 10px 18px;
    background: var(--accent);
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 1rem;
    cursor: pointer;
    font-weight: 600;
    transition: background 0.2s;
  }

  .btn-add:hover {
    background: var(--accent-dark);
  }

  .stats-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 12px;
    gap: 12px;
  }

  .stats {
    font-size: 0.85rem;
    color: var(--text-muted);
  }

  .btn-clear-completed {
    padding: 6px 12px;
    background: transparent;
    color: var(--text-muted);
    border: 1px solid var(--border);
    border-radius: 6px;
    font-size: 0.8rem;
    cursor: pointer;
    white-space: nowrap;
    transition: color 0.2s, border-color 0.2s, background 0.2s;
  }

  .btn-clear-completed:hover {
    color: var(--text);
    border-color: var(--accent);
    background: var(--border);
  }

  .filter-tabs {
    display: flex;
    gap: 6px;
    margin-bottom: 16px;
  }

  .filter-tab {
    flex: 1;
    padding: 8px 12px;
    background: var(--surface);
    color: var(--text-muted);
    border: 1px solid var(--border);
    border-radius: 6px;
    font-size: 0.85rem;
    cursor: pointer;
    font-weight: 500;
    transition: all 0.2s;
  }

  .filter-tab:hover {
    border-color: var(--accent);
  }

  .filter-tab.active {
    background: var(--sidebar-active);
    color: var(--sidebar-active-text);
    border-color: var(--sidebar-active);
  }

  .todos {
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .todos li {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px 14px;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 10px;
    transition: background 0.2s, border-color 0.2s;
  }

  .todos li.completed .todo-text {
    text-decoration: line-through;
    color: var(--text-muted);
  }

  .todos li input[type="checkbox"] {
    width: 18px;
    height: 18px;
    cursor: pointer;
    accent-color: var(--accent);
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
    background: var(--surface);
    color: var(--text);
    border: 2px solid var(--accent);
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

  button:hover {
    opacity: 0.85;
  }

  .btn-edit {
    background: var(--sidebar-active);
    color: var(--sidebar-active-text);
  }

  .btn-delete {
    background: #fee2e2;
    color: #dc2626;
  }

  main.dark .btn-delete {
    background: #7f1d1d;
    color: #fca5a5;
  }

  .btn-save {
    background: #d1fae5;
    color: #065f46;
  }

  main.dark .btn-save {
    background: #064e3b;
    color: #a7f3d0;
  }

  .btn-cancel {
    background: var(--border);
    color: var(--text-muted);
  }

  .empty {
    text-align: center;
    color: var(--text-muted);
    font-size: 0.95rem;
    padding: 24px 0;
  }

  /* Responsive Design */
  @media (max-width: 768px) {
    .layout {
      flex-direction: column;
    }

    .mobile-overlay {
      display: block;
    }

    .mobile-header {
      display: flex;
    }

    .btn-menu {
      display: flex;
    }

    .sidebar {
      position: fixed;
      left: 0;
      top: 0;
      width: 280px;
      height: 100vh;
      z-index: 100;
      border-right: 1px solid var(--border);
      transform: translateX(-100%);
      transition: transform 0.3s ease;
      border-radius: 0;
      padding-top: 16px;
    }

    .sidebar.open {
      transform: translateX(0);
    }

    .btn-close-sidebar {
      display: flex;
    }

    .content {
      padding: 20px 16px;
    }

    h1 {
      font-size: 1.5rem;
      margin-bottom: 16px;
    }

    .input-row input {
      padding: 10px 12px;
      font-size: 16px;
    }

    .stats-row {
      flex-direction: column;
      align-items: stretch;
      gap: 8px;
    }

    .stats {
      font-size: 0.8rem;
      text-align: center;
    }

    .btn-clear-completed {
      width: 100%;
      text-align: center;
    }

    .filter-tabs {
      gap: 4px;
      margin-bottom: 12px;
    }

    .filter-tab {
      padding: 6px 8px;
      font-size: 0.8rem;
    }

    .actions {
      gap: 4px;
    }

    button {
      padding: 6px 10px;
      font-size: 0.8rem;
    }

    .todo-text {
      font-size: 0.95rem;
    }

    .btn-add {
      padding: 10px 14px;
      font-size: 0.95rem;
    }
  }

  @media (max-width: 480px) {
    .layout {
      max-width: 100%;
    }

    .content {
      padding: 16px 12px;
    }

    h1 {
      font-size: 1.3rem;
      margin-bottom: 12px;
    }

    .input-row {
      gap: 6px;
      margin-bottom: 8px;
    }

    .input-row input {
      padding: 8px 10px;
      font-size: 16px;
      border-radius: 6px;
    }

    .stats-row {
      gap: 6px;
    }

    .filter-tabs {
      gap: 4px;
      margin-bottom: 12px;
    }

    .filter-tab {
      padding: 6px 6px;
      font-size: 0.75rem;
    }

    .btn-add {
      padding: 8px 12px;
      font-size: 0.9rem;
      border-radius: 6px;
    }

    .todos li {
      padding: 10px 12px;
      gap: 8px;
      border-radius: 8px;
    }

    .todos li input[type="checkbox"] {
      width: 16px;
      height: 16px;
    }

    .actions {
      gap: 3px;
    }

    button {
      padding: 5px 8px;
      font-size: 0.75rem;
      border-radius: 4px;
    }

    .sidebar {
      width: 250px;
    }

    .lists-header h2 {
      font-size: 1rem;
    }

    .new-list-row input {
      font-size: 0.85rem;
      padding: 6px 10px;
    }

    .list-item {
      font-size: 0.9rem;
      padding: 8px 10px;
    }
  }
</style>
