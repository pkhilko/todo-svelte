# 📝 Todoo - Advanced Todo App

A modern, feature-rich todo list application built with **Svelte 5** and **Vite**. Manage multiple lists, set priorities, track due dates, and organize your tasks with an intuitive interface.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Svelte](https://img.shields.io/badge/svelte-5.53.7-FF3E00?style=flat-square)
![Vite](https://img.shields.io/badge/vite-8.0.0-646CFF?style=flat-square)

## 🌐 Try It Online

### **[👉 Live Demo: https://todo-svelte2026.netlify.app/](https://todo-svelte2026.netlify.app/)**

No installation needed! Just open the link and start organizing your todos. Your data is stored locally in your browser.

## ✨ Features

### Core Features
- 📋 **Multiple Todo Lists** - Create and manage separate lists for different projects/contexts
- 🎨 **Dark/Light Theme** - Toggle between dark and light modes with persistent preference
- 📱 **Fully Responsive** - Desktop, tablet, and mobile optimized design
- 💾 **Local Storage Persistence** - All data saves automatically to browser storage

### Tier 1 - Organization
- 🏷️ **Filter Todos** - View All, Active, or Completed todos with tab filtering
- 🗑️ **Clear Completed** - Bulk delete all completed todos in one click
- ✏️ **Rename Lists** - Double-click any list name to edit it inline
- 📊 **Stats Counter** - See remaining vs total todos at a glance

### Tier 2 - Task Management
- 🔴 **Priority Levels** - Set High, Medium, or Low priority with color-coded visual indicators
- 📅 **Due Dates** - Assign deadlines with visual tags (today = yellow, overdue = red)
- 🔍 **Search/Filter** - Real-time search by todo text across all lists
- ⚠️ **Overdue Detection** - Automatically highlights past due dates

### Tier 3 - Advanced UX
- 🔀 **Drag-and-Drop** - Reorder todos and lists by dragging (changes persist)
- ⌨️ **Keyboard Shortcuts** - Press `/` anywhere to focus the input field
- ✋ **Delete Confirmation** - Shows todo count when deleting a list to prevent accidents

## 🚀 Quick Start

### Prerequisites
- Node.js 16+ and npm/yarn

### Installation

```bash
# Clone the repository
git clone https://github.com/pkhilko/todo-svelte.git
cd todo-svelte

# Install dependencies
npm install

# Start development server
npm run dev
```

The app will be available at `http://localhost:5173`

### Build for Production

```bash
# Create optimized build
npm run build

# Preview production build locally
npm run preview
```

## 📦 Deployment

### Deploy to Netlify (Recommended) ⭐

1. Go to [netlify.com](https://netlify.com) and sign up (free)
2. Click **"New site from Git"**
3. Select GitHub and authorize
4. Choose this repository (`pkhilko/todo-svelte`)
5. Configure build settings:
   - **Build command:** `npm run build`
   - **Publish directory:** `dist`
6. Click **Deploy** - Done! 🎉

Your app will be live at `https://your-app-name.netlify.app`

### Deploy to Vercel

1. Go to [vercel.com](https://vercel.com) and sign up
2. Click **"Import Project"**
3. Select GitHub and choose this repository
4. Vercel auto-detects Vite settings
5. Click **Deploy** - Done! 🎉

### Deploy to GitHub Pages

1. Update `vite.config.js`:
```js
export default {
  base: '/todo-svelte/',
  // ... rest of config
}
```

2. Deploy:
```bash
npm run build
git add dist -f
git commit -m "Deploy to GitHub Pages"
git push origin gh-pages
```

## 💻 Tech Stack

- **Frontend Framework:** Svelte 5.53.7
- **Build Tool:** Vite 8.0.0
- **Styling:** CSS 3 with CSS Variables
- **State Management:** Svelte Reactive Variables
- **Persistence:** Browser localStorage API
- **Bundle Size:** ~50KB JS, ~13.5KB CSS (gzipped: 19KB, 2.48KB)

## 📋 Project Structure

```
src/
├── App.svelte      # Main application component (all-in-one)
└── main.js         # Entry point

dist/               # Production build (generated)
public/             # Static assets
index.html          # HTML entry point
```

## 🎯 How to Use

### Create a List
1. Click the **"+"** button in the sidebar
2. Type your list name (e.g., "Work", "Shopping", "Personal")
3. Press Enter or click the plus icon

### Add a Todo
1. Type in the **"What needs to be done?"** input (or press `/` to focus)
2. (Optional) Select priority: Low, Medium, or High
3. (Optional) Pick a due date
4. Click **Add** or press Enter

### Manage Todos
- **Toggle Complete:** Click the checkbox
- **Edit:** Click Edit button
- **Delete:** Click Delete button
- **Reorder:** Drag and drop to reorder
- **Filter:** Use All / Active / Completed tabs
- **Search:** Type in the search input to find todos by text

### Manage Lists
- **Switch List:** Click a list name in the sidebar
- **Rename List:** Double-click the list name to edit
- **Delete List:** Click × button (shows confirmation with count)
- **Reorder Lists:** Drag and drop in sidebar

### Other Features
- **Toggle Theme:** Click ☀/☾ button in sidebar header
- **Quick Focus:** Press `/` anywhere to focus the input
- **Bulk Clear:** Click "Clear completed" to remove all done todos

## 🎨 Customization

### Change Colors

Edit the CSS variables in `src/App.svelte` (around line 227):

```css
:root {
  /* Light theme */
  --bg: #ffffff;
  --accent: #6c63ff;
  /* ... more variables */
}

main.dark {
  /* Dark theme */
  --bg: #1a1a2e;
  --accent: #a5b4fc;
  /* ... more variables */
}
```

## 💾 Data Storage

All data is stored in **browser localStorage** under the key `todoo-data`. This includes:
- Lists and todos with all metadata
- Priorities and due dates
- Active list selection
- Dark mode preference
- Unique ID counters

**Important Notes:**
- ✅ Data persists across sessions
- ❌ Not synced across devices/browsers
- ❌ No cloud backup (local only)
- 💡 Use browser DevTools to inspect: `localStorage.getItem('todoo-data')`

## 🔧 Development

### Development Server
```bash
npm run dev
```
Runs hot reload at `http://localhost:5173`

### Production Build
```bash
npm run build
```
Outputs optimized files to `dist/` folder

### Preview Production Build
```bash
npm run preview
```
Test the built version locally before deploying

## 📊 Performance

- **Initial Load:** ~2-3 seconds
- **Bundle Size:** ~50KB JS (19KB gzipped)
- **CSS Size:** ~13.5KB (2.48KB gzipped)
- **No external dependencies** for runtime (only dev tools)
- **Runs entirely in browser** (no backend needed)

## 🐛 Known Limitations

- Data stored locally (not synced across devices)
- No backend/cloud synchronization
- No user accounts or authentication
- No export/import functionality
- Limited to browser storage limits (~5-10MB)

## 🚧 Future Enhancements

- [ ] Cloud sync with user accounts
- [ ] Export/import (JSON, CSV)
- [ ] Recurring todos and reminders
- [ ] Tags and color-coded categories
- [ ] Todo notes and descriptions
- [ ] Subtasks and checklists
- [ ] Dark mode auto-schedule
- [ ] PWA support (offline use)
- [ ] Collaborative lists (shared access)

## 📄 License

This project is open source under the [MIT License](LICENSE).

## 👤 Author

**Pkhilko**
- GitHub: [@pkhilko](https://github.com/pkhilko)
- Repository: [todo-svelte](https://github.com/pkhilko/todo-svelte)

## 🤝 Contributing

Contributions are welcome! Here's how:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Make your changes
4. Commit: `git commit -m 'Add your feature'`
5. Push: `git push origin feature/your-feature`
6. Open a Pull Request

## 📞 Support & Feedback

- 🐛 Found a bug? Open a [GitHub Issue](https://github.com/pkhilko/todo-svelte/issues)
- 💡 Have an idea? Start a [Discussion](https://github.com/pkhilko/todo-svelte/discussions)
- ⭐ Like the project? Please star it!

---

**Built with ❤️ using Svelte 5 and Vite** | Last Updated: 2026
