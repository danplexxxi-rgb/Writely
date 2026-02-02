<template>
  <div class="app-shell">
    <header class="topbar">
      <div class="brand">
        <div class="logo">W</div>
        <div>
          <p class="brand-title">Writely Studio</p>
          <p class="brand-subtitle">Professional manuscript formatting</p>
        </div>
        <span class="status-pill">
          <i class="ri-cloud-line" aria-hidden="true"></i>
          Auto-saved 1 min ago
        </span>
      </div>
      <nav class="topbar-actions">
        <button class="ghost-button">
          <i class="ri-folder-open-line" aria-hidden="true"></i>
          Import
        </button>
        <button class="ghost-button">
          <i class="ri-image-add-line" aria-hidden="true"></i>
          Insert media
        </button>
        <button class="ghost-button">
          <i class="ri-chat-1-line" aria-hidden="true"></i>
          Notes
        </button>
        <button class="primary-button">Export EPUB</button>
        <button class="avatar">LS</button>
      </nav>
    </header>

    <div class="workspace">
      <aside class="sidebar">
        <div class="sidebar-section">
          <h3>Manuscript map</h3>
          <ul class="section-list">
            <li v-for="item in sections" :key="item.title" :class="{ active: item.active }">
              <span class="section-type">{{ item.type }}</span>
              <div>
                <p class="section-title">{{ item.title }}</p>
                <p class="section-meta">{{ item.meta }}</p>
              </div>
            </li>
          </ul>
        </div>

        <div class="sidebar-section">
          <h3>Assets</h3>
          <div class="asset-card" v-for="asset in assets" :key="asset.name">
            <div class="asset-icon"><i :class="asset.icon"></i></div>
            <div>
              <p class="asset-name">{{ asset.name }}</p>
              <p class="asset-meta">{{ asset.meta }}</p>
            </div>
          </div>
        </div>

        <div class="sidebar-section">
          <h3>Checklist</h3>
          <ul class="checklist">
            <li v-for="item in checklist" :key="item.label">
              <span :class="['check-dot', item.done ? 'done' : '']"></span>
              <div>
                <p class="check-title">{{ item.label }}</p>
                <p class="check-meta">{{ item.meta }}</p>
              </div>
            </li>
          </ul>
        </div>
      </aside>

      <main class="editor">
        <section class="editor-header">
          <div>
            <h1>{{ activeChapter.title }}</h1>
            <p class="editor-subtitle">{{ activeChapter.subtitle }}</p>
          </div>
          <div class="editor-controls">
            <button class="chip">
              <i class="ri-quill-pen-line"></i>
              Draft mode
            </button>
            <button class="chip chip-outline">Auto-formatting on</button>
            <button class="chip chip-outline">11pt · 6x9 in</button>
          </div>
        </section>

        <section class="editor-body">
          <article class="page">
            <header class="page-header">
              <p class="page-label">Chapter {{ activeChapter.number }}</p>
              <h2>{{ activeChapter.title }}</h2>
              <p class="page-subtitle">{{ activeChapter.subtitle }}</p>
            </header>
            <div class="toolbar">
              <button class="tool"><i class="ri-bold"></i></button>
              <button class="tool"><i class="ri-italic"></i></button>
              <button class="tool"><i class="ri-align-left"></i></button>
              <button class="tool"><i class="ri-align-center"></i></button>
              <button class="tool"><i class="ri-separator"></i></button>
              <button class="tool"><i class="ri-footprint-line"></i></button>
              <button class="tool"><i class="ri-book-open-line"></i></button>
            </div>
            <p v-for="paragraph in activeChapter.paragraphs" :key="paragraph" class="page-paragraph">
              {{ paragraph }}
            </p>
            <blockquote class="page-quote">
              "Scene ornaments, drop caps, and automatic pagination are handled for every export format with
              fine-grained overrides at the section level."
            </blockquote>
            <figure class="page-figure">
              <img
                src="https://images.unsplash.com/photo-1500530855697-b586d89ba3ee?auto=format&fit=crop&w=800&q=80"
                alt="Forest landscape"
              />
              <figcaption>Full-bleed image with automatic caption styling.</figcaption>
            </figure>
            <p class="page-paragraph">
              The layout engine ensures widows, orphans, and section breaks are handled with industry standards
              across both EPUB and print-ready PDF exports.
            </p>
          </article>

          <aside class="editor-panel">
            <div class="panel-card">
              <h3>Formatting presets</h3>
              <div class="preset" v-for="preset in presets" :key="preset.name">
                <div>
                  <p class="preset-title">{{ preset.name }}</p>
                  <p class="preset-meta">{{ preset.meta }}</p>
                </div>
                <button class="chip chip-outline">{{ preset.action }}</button>
              </div>
            </div>

            <div class="panel-card">
              <h3>Navigator</h3>
              <ul class="panel-list">
                <li v-for="item in navigator" :key="item.label">
                  <span>{{ item.label }}</span>
                  <span :class="['status', item.statusClass]">{{ item.status }}</span>
                </li>
              </ul>
            </div>

            <div class="panel-card">
              <h3>Export status</h3>
              <div class="export-row" v-for="exportItem in exports" :key="exportItem.title">
                <div>
                  <p class="export-title">{{ exportItem.title }}</p>
                  <p class="export-meta">{{ exportItem.meta }}</p>
                </div>
                <button :class="exportItem.buttonClass">{{ exportItem.button }}</button>
              </div>
            </div>

            <div class="panel-card">
              <h3>Production insights</h3>
              <div class="insight">
                <span class="insight-value">92k</span>
                <span class="insight-label">Words</span>
              </div>
              <div class="insight">
                <span class="insight-value">312</span>
                <span class="insight-label">Pages est.</span>
              </div>
              <div class="insight">
                <span class="insight-value">14</span>
                <span class="insight-label">Images</span>
              </div>
            </div>
          </aside>
        </section>
      </main>
    </div>
  </div>
</template>

<script setup lang="ts">
const sections = [
  {
    type: 'Front matter',
    title: 'Title page',
    meta: 'Auto-generated · 1 page',
    active: false,
  },
  {
    type: 'Front matter',
    title: 'Dedication',
    meta: 'Optional',
    active: false,
  },
  {
    type: 'Chapter',
    title: 'Chapter 3 · Tidal Patterns',
    meta: '2,450 words',
    active: false,
  },
  {
    type: 'Chapter',
    title: 'Chapter 4 · Signals',
    meta: '3,120 words',
    active: true,
  },
  {
    type: 'Back matter',
    title: 'Acknowledgements',
    meta: 'Draft',
    active: false,
  },
];

const assets = [
  {
    name: 'Cover art',
    meta: '2400 × 3600px',
    icon: 'ri-image-line',
  },
  {
    name: 'Illustrations',
    meta: '8 images · optimized',
    icon: 'ri-landscape-line',
  },
  {
    name: 'Author photo',
    meta: 'Square · grayscale',
    icon: 'ri-camera-line',
  },
];

const checklist = [
  {
    label: 'Front matter verified',
    meta: 'ISBN, copyright, dedication',
    done: true,
  },
  {
    label: 'Scene breaks consistent',
    meta: 'Auto ornaments + manual overrides',
    done: true,
  },
  {
    label: 'Back matter pending',
    meta: 'Index + about the author',
    done: false,
  },
];

const presets = [
  {
    name: 'Modern Serif',
    meta: '11pt / 1.4 line height / 5x8 in',
    action: 'Active',
  },
  {
    name: 'Classic Hardcover',
    meta: '12pt / 1.5 line height / 6x9 in',
    action: 'Preview',
  },
];

const navigator = [
  {
    label: 'Front matter',
    status: 'Complete',
    statusClass: 'complete',
  },
  {
    label: 'Chapter 4',
    status: 'In progress',
    statusClass: 'pending',
  },
  {
    label: 'Back matter',
    status: 'Not started',
    statusClass: 'idle',
  },
];

const exports = [
  {
    title: 'EPUB 3',
    meta: 'Last generated 12 minutes ago',
    button: 'Download',
    buttonClass: 'primary-button small',
  },
  {
    title: 'Print-ready PDF',
    meta: 'Needs reflow after edits',
    button: 'Regenerate',
    buttonClass: 'ghost-button small',
  },
];

const activeChapter = {
  number: 4,
  title: 'Signals from the Harbor',
  subtitle: 'Layering tension with scene breaks and ornaments',
  paragraphs: [
    'Writely Studio balances a clean writing surface with the precision of professional typography. As you type, it anticipates section breaks, drop caps, and chapter ornaments with default behaviors you can override.',
    'Inline notes, footnotes, and scene dividers can be inserted in a single click. Each element is tracked by the production engine so that EPUB and PDF exports remain faithful to the manuscript structure.',
    'The manuscript navigator highlights front matter, core chapters, and back matter in a single view. This keeps long projects organized and provides a clear export checklist.',
  ],
};
</script>
