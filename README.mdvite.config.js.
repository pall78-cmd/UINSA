import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// PENTING: Ganti 'linktree-uinsa' jika nama repository GitHub Anda berbeda
const repoName = 'linktree-uinsa'; 

export default defineConfig({
  plugins: [react()],
  // Tambahkan base path agar aset dimuat dari /linktree-uinsa/
  base: `/${repoName}/`, 
  server: {
    port: 5173
  }
})

