#!/usr/bin/env bash
set -euo pipefail

# Ubah variabel ini jika mau
OWNER="pall78-cmd"
REPO="linktree-uinsa"
VISIBILITY="public" # public or private

printf "This script will create project files, git commit, and (optionally) create a GitHub repo and push.\n"
read -p "Continue? (y/N) " confirm
if [[ "${confirm,,}" != "y" ]]; then
  echo "Aborted."
  exit 1
fi

echo "Creating files..."
mkdir -p src/components

cat > package.json <<'EOF'
{
  "name": "linktree-uinsa",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "autoprefixer": "^10.4.14",
    "postcss": "^8.4.23",
    "tailwindcss": "^3.4.7",
    "vite": "^5.0.0"
  }
}
EOF

cat > vite.config.js <<'EOF'
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173
  }
})
EOF

cat > index.html <<'EOF'
<!doctype html>
<html lang="id">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <title>Linktree UIN Sunan Ampel Surabaya</title>
    <meta name="description" content="Linkhub UIN Sunan Ampel Surabaya — website, PMB, fakultas, dan sosial media." />
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <!-- FontAwesome CDN (used for icons) -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" crossorigin="anonymous" referrerpolicy="no-referrer" />
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
EOF

cat > src/main.jsx <<'EOF'
import React from 'react'
import { createRoot } from 'react-dom/client'
import App from './App'
import './index.css'

createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)
EOF

cat > src/App.jsx <<'EOF'
import React from 'react'
import Linktree from './components/Linktree'

export default function App() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gradient-to-br from-emerald-900 to-emerald-600 p-4 text-white">
      <Linktree />
    </div>
  )
}
EOF

cat > src/components/Linktree.jsx <<'EOF'
import React, { useState } from 'react'

const LOGO_BASE64 = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEAAAABACAYAAADmBMqtAAAFKklEQVR4nO2by49VVRCGz2W0Ld9xQ0xMjL9pLp84jH8AQX8AmXjBfxA/xAQeN5o4iI84b3eQp9eQyRkGgT/g+08B3Q0W1k7g/gSbeGg1jM/7o6XoQvS9g8Fj3s05/r85513n3t6Xl0/N/R1b9OqrKqqKik/bVj1bK/d4qjJqq7+O3eE0+8L7fN9G2q2/60N0Gf/2oZ/8WjDq9pQ/Xo76aD6tXbSX+tI+m0N0N+/j3S/p+q3U+rG/3fVf/8U/X+kKr3d73/60fV/3fT+wN77f0Dq2776d/36f8H+t97711d9f8A3q+h9b6n6t//738H+N97711d9f8A3q+h9b6n6r/98/f/J+Vl8N/Q/p+9/yA/x/r/X/n0+n087f9oD+l/b//eQ+0v/0H+H//9j6b/o//oD+v/3/n0+n087f9oD+l/b//eQ+0v/0H+H//9j6b/o//oD+v/3/n0+n087f9oD+l/b//eQ+0v/0H+H//9j6b/o//oD+v/3/n0+n087f9oD+l/b//eQ+0v/0H+H//9j6b/o//oD+v/3/17/wD/X/u9p/Q4AAAAASUVORK5CYII="

const FACULTIES = [
  { name: "Fakultas Tarbiyah & Keguruan", url: "https://uinsa.ac.id/ftk", icon: "fa-chalkboard-teacher" },
  { name: "Fakultas Adab & Humaniora", url: "https://uinsa.ac.id/faculty-of-adab-and-humanities", icon: "fa-book-open" },
  { name: "Fakultas Dakwah & Komunikasi", url: "https://uinsa.ac.id/fdk", icon: "fa-broadcast-tower" },
  { name: "Fakultas Syariah & Hukum", url: "https://uinsa.ac.id/fsh", icon: "fa-balance-scale" },
  { name: "Fakultas Ekonomi & Bisnis Islam", url: "https://uinsa.ac.id/febi", icon: "fa-coins" },
  { name: "Fakultas Sains & Teknologi", url: "https://uinsa.ac.id/fst", icon: "fa-microscope" },
  { name: "Fakultas Ilmu Sosial & Ilmu Politik", url: "https://uinsa.ac.id/fisip", icon: "fa-users" },
  { name: "Fakultas Psikologi & Kesehatan", url: "https://uinsa.ac.id/fpk", icon: "fa-brain" },
  { name: "Fakultas Ushuluddin & Filsafat", url: "https://uinsa.ac.id/fuf", icon: "fa-kaaba" },
  { name: "Pascasarjana (S2 & S3)", url: "https://uinsa.ac.id/pascasarjana", icon: "fa-graduation-cap", highlight: true }
]

export default function Linktree() {
  const [openIndex, setOpenIndex] = useState(null)

  function toggle(index) {
    setOpenIndex(prev => (prev === index ? null : index))
  }

  return (
    <main className="w-full max-w-md mx-auto" aria-label="Link hub UIN Sunan Ampel Surabaya">
      <a className="skip-link sr-only-focusable" href="#links">Lewati ke konten</a>

      <header className="flex flex-col items-center mb-8 text-center">
        <a
          href="https://uinsa.ac.id/"
          target="_blank"
          rel="noopener noreferrer"
          className="rounded-full transition-transform transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-yellow-300"
          aria-label="Kunjungi website resmi UIN Sunan Ampel Surabaya"
        >
          <div className="w-24 h-24 bg-transparent rounded-full flex items-center justify-center mb-4 border-4 border-yellow-400 overflow-hidden shadow-lg p-2">
            <img src={LOGO_BASE64} alt="Logo UIN Sunan Ampel Surabaya" loading="lazy" className="w-full h-full object-contain" width="96" height="96" />
          </div>
        </a>

        <h1 className="text-xl font-bold tracking-wide">UIN Sunan Ampel Surabaya</h1>
        <p className="text-sm text-gray-200 mt-1">The Leading Community-Engaged University</p>

        <nav aria-label="Sosial media UINSA" className="flex gap-4 mt-4">
          <a href="https://www.instagram.com/uinsa.official/" target="_blank" rel="noopener noreferrer" className="text-white hover:text-yellow-400 text-2xl transition focus:outline-none focus:ring-2 focus:ring-yellow-300" aria-label="Instagram UINSA">
            <i className="fab fa-instagram" aria-hidden="true"></i>
          </a>
          <a href="https://www.youtube.com/@uinsa.official" target="_blank" rel="noopener noreferrer" className="text-white hover:text-red-500 text-2xl transition focus:outline-none focus:ring-2 focus:ring-red-300" aria-label="YouTube UINSA">
            <i className="fab fa-youtube" aria-hidden="true"></i>
          </a>
          <a href="https://uinsa.ac.id" target="_blank" rel="noopener noreferrer" className="text-white hover:text-blue-400 text-2xl transition focus:outline-none focus:ring-2 focus:ring-blue-300" aria-label="Situs web UINSA">
            <i className="fas fa-globe" aria-hidden="true"></i>
          </a>
        </nav>
      </header>

      <section id="links" className="mb-6">
        <h2 className="text-sm font-bold text-yellow-400 uppercase tracking-wider mb-3 text-center">Informasi Utama</h2>

        <div className="space-y-3">
          <a href="https://uinsa.ac.id/" target="_blank" rel="noopener noreferrer" className="w-full bg-white text-green-900 font-semibold py-4 px-6 rounded-xl mb-3 flex items-center justify-between shadow-md focus:outline-none focus:ring-4 focus:ring-yellow-300">
            <span className="flex items-center gap-3">
              <i className="fas fa-university text-xl w-6" aria-hidden="true"></i>
              Website Resmi UINSA
            </span>
            <i className="fas fa-external-link-alt text-gray-400 text-sm" aria-hidden="true"></i>
          </a>

          <a href="https://pmb.uinsa.ac.id/" target="_blank" rel="noopener noreferrer" className="w-full bg-yellow-400 hover:bg-yellow-300 text-green-900 font-bold py-4 px-6 rounded-xl mb-3 flex items-center justify-between shadow-md border-2 border-yellow-500 focus:outline-none focus:ring-4 focus:ring-yellow-300">
            <span className="flex items-center gap-3">
              <i className="fas fa-user-graduate text-xl w-6" aria-hidden="true"></i>
              Penerimaan Mahasiswa Baru (PMB)
            </span>
            <i className="fas fa-arrow-right" aria-hidden="true"></i>
          </a>
        </div>
      </section>

      <section aria-labelledby="fakultas-heading" className="mb-8">
        <h2 id="fakultas-heading" className="text-sm font-bold text-yellow-400 uppercase tracking-wider mb-3 text-center">Fakultas & Program</h2>

        <div className="space-y-3">
          {FACULTIES.map((f, i) => (
            <div key={f.name} className={`rounded-lg ${f.highlight ? 'border-l-4 border-yellow-400' : ''}`}>
              <button
                aria-expanded={openIndex === i}
                aria-controls={`panel-${i}`}
                id={`accordion-${i}`}
                onClick={() => toggle(i)}
                className="w-full glass-card text-white font-medium py-3 px-5 rounded-lg flex items-center justify-between gap-3 hover:bg-white hover:text-green-900 transition link-btn focus:outline-none focus:ring-4 focus:ring-yellow-300"
              >
                <span className="flex items-center gap-3">
                  <i className={`fas ${f.icon} w-6 text-center`} aria-hidden="true"></i>
                  <span>{f.name}</span>
                </span>

                <span className="flex items-center gap-2">
                  <a
                    href={f.url}
                    target="_blank"
                    rel="noopener noreferrer"
                    onClick={e => e.stopPropagation()}
                    className="text-gray-300 hover:text-gray-600 focus:outline-none focus:ring-2 focus:ring-yellow-300 rounded px-2 py-1"
                    aria-label={`Buka ${f.name}`}
                  >
                    <i className="fas fa-arrow-up-right-from-square" aria-hidden="true"></i>
                  </a>

                  <i className={`fas fa-chevron-${openIndex === i ? 'up' : 'down'} ml-1`} aria-hidden="true"></i>
                </span>
              </button>

              <div
                id={`panel-${i}`}
                role="region"
                aria-labelledby={`accordion-${i}`}
                hidden={openIndex !== i}
                className="mt-2 px-4 pb-3 text-gray-200"
              >
                <p className="text-sm">
                  Kunjungi halaman fakultas untuk informasi program studi, dosen, dan kegiatan.
                </p>
                <p className="text-xs mt-2 text-gray-300">
                  Langsung ke: <a href={f.url} target="_blank" rel="noopener noreferrer" className="underline">{f.url}</a>
                </p>
              </div>
            </div>
          ))}
        </div>
      </section>

      <footer className="mt-4 mb-10 text-center">
        <a href="https://www.instagram.com/uinsa.official/" target="_blank" rel="noopener noreferrer" className="inline-flex items-center gap-2 text-white bg-gradient-to-r from-purple-500 to-pink-500 hover:from-purple-600 hover:to-pink-600 px-6 py-2 rounded-full font-bold shadow-lg transition transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-pink-300" aria-label="Ikuti Instagram UINSA">
          <i className="fab fa-instagram" aria-hidden="true"></i>
          <span>Follow @uinsa.official</span>
        </a>
        <p className="text-xs text-gray-300 mt-6">© 2024 Unofficial Linktree Concept</p>
      </footer>
    </main>
  )
}
EOF

cat > src/index.css <<'EOF'
@tailwind base;
@tailwind components;
@tailwind utilities;

/* Custom utilities */
:root {
  --glass-bg: rgba(255,255,255,0.08);
  --glass-border: rgba(255,255,255,0.12);
}

/* Glass card */
.glass-card {
  background: var(--glass-bg);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  border: 1px solid var(--glass-border);
}

/* Link button hover animation */
.link-btn {
  transition: transform .18s ease, box-shadow .18s ease;
}
.link-btn:hover, .link-btn:focus {
  transform: translateY(-3px);
  box-shadow: 0 10px 20px rgba(2,6,23,0.25);
}

/* Skip link visibility helper */
.skip-link {
  position: absolute;
  left: 1rem;
  top: 1rem;
  background: #fff;
  color: #064e3b;
  padding: .5rem .75rem;
  border-radius: .375rem;
  transform: translateY(-120%);
  transition: transform .2s ease;
  z-index: 50;
}
.skip-link:focus {
  transform: translateY(0%);
}

/* Utility to show skip link when focused */
.sr-only-focusable {
  position: absolute;
  left: -9999px;
  width: 1px;
  height: 1px;
  overflow: hidden;
}
.sr-only-focusable:focus {
  left: 1rem;
  top: 1rem;
  width: auto;
  height: auto;
  overflow: visible;
}

/* Small tweaks */
body {
  font-family: Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
}
EOF

cat > tailwind.config.cjs <<'EOF'
module.exports = {
  content: [
    "./index.html",
    "./src/**/*.{js,jsx,ts,tsx}"
  ],
  theme: {
    extend: {}
  },
  plugins: []
}
EOF

cat > postcss.config.cjs <<'EOF'
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {}
  }
}
EOF

cat > README.md <<'EOF'
# Linktree UINSA — React + Vite + Tailwind

Proyek kecil: konversi Linktree (UIN Sunan Ampel Surabaya) menjadi aplikasi React dengan:
- Aksesibilitas (aria, skip link, fokus)
- Accordion fakultas interaktif dan keyboard-friendly
- Konfigurasi Tailwind + Vite
- Optimalisasi small improvements (lazy img, rel on links)

## Persyaratan
- Node.js 18+ dan npm/yarn/pnpm

## Instalasi
1. Install dependensi:
   npm install
   atau
   yarn

2. Jalankan dev server:
   npm run dev
   atau
   yarn dev

3. Build untuk produksi:
   npm run build

4. Preview hasil build:
   npm run preview

## Struktur singkat
- index.html — HTML entry
- src/
  - main.jsx — mount React
  - App.jsx — wrapper
  - components/Linktree.jsx — komponen utama
  - index.css — Tailwind + custom styles
- tailwind.config.cjs, postcss.config.cjs — konfigurasi

## Catatan
- Ikon pakai FontAwesome CDN di index.html.
- Logo disematkan sebagai Base64 inline (sudah ada).
- Jika mau, saya bisa:
  - Menambahkan i18n (Bahasa ID / EN toggle).
  - Membuat versi statis HTML/CSS tanpa React.
  - Deploy ke Vercel / Netlify dan bantu konfigurasi domain.
EOF

cat > .gitignore <<'EOF'
node_modules
dist
.env
EOF

cat > LICENSE <<'EOF'
MIT License

Copyright (c) 2025 pall78-cmd

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
EOF

echo "Files created."

# Initialize git and commit
if [ ! -d ".git" ]; then
  git init
fi

git add .
git commit -m "Initial commit: add project files" || echo "Nothing to commit"

# Ensure main branch
git branch -M main || true

# Try to create GH repo with gh (if available)
if command -v gh >/dev/null 2>&1; then
  echo "gh CLI found — creating GitHub repository ${OWNER}/${REPO} (${VISIBILITY})..."
  # Try to create; ignore error if exists
  gh repo create "${OWNER}/${REPO}" --"${VISIBILITY}" --source=. --remote=origin --push || echo "gh repo create failed or repo exists; attempting to push to existing remote..."
else
  echo "GitHub CLI (gh) not found. Please create a repository named ${REPO} under ${OWNER}, then add remote and push:"
  echo ""
  echo "  git remote add origin https://github.com/${OWNER}/${REPO}.git"
  echo "  git push -u origin main"
  echo ""
  echo "Or install GitHub CLI and run this script again."
  exit 0
fi

echo "Done. If gh created the repo and pushed, visit: https://github.com/${OWNER}/${REPO}"
echo "Next: run 'npm install' and 'npm run dev' to start the dev server."
EOF
