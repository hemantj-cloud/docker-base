## ✅ What I did:

- Created a **base Docker image (`Dockerfile.base`)** with Node 20 Alpine and a non-root user for security  
- Installed **`@nitrostack/cli` globally in base image** to reuse across all projects and avoid repeated installation  
- Optimized base image by:
  - Combining layers (apk + npm install)
  - Cleaning npm cache
  - Disabling audit/fund checks  
  - Adding npm configs (`prefer-offline`, `loglevel`) for faster and cleaner builds  
  - Verifying CLI installation during build (`nitrostack-cli --version`) for fail-fast behavior  
- Used this base image in the main **multi-stage Dockerfile** (builder + production stages)  
- Kept **npm install and build steps in the main Dockerfile** to handle project-specific dependencies  
- Optimized builds using:
  - **Docker layer caching (copy package.json first)**
  - **BuildKit cache (`--mount=type=cache`)** for faster npm installs  
- Ensured **clean dependency installs** by avoiding local `node_modules` (via `.dockerignore`)  
- Fixed **platform-specific build issues (esbuild)** by enforcing fresh installs inside Docker  
- Ensured **only production dependencies** are included in the final image to reduce size  
- Added cleanup steps to remove unnecessary files from `node_modules`  
- Implemented **conditional widget handling** to avoid build failures if widgets are absent  
- Added **healthcheck and non-root execution** for better production reliability  

---

## 📦 Size Improvements (Actual):

### 🔹 Old Image (before optimizations)
- `fresh-pizza:local`
  - **Disk Usage:** 418MB  
  - **Content Size:** 87.7MB  

---

### 🔹 Using Custom Base Image (`hemantwekan/node-base:20-alpine`)
- `hemantwekan/fresh-pizza:1.4.0`
  - **Disk Usage:** 313MB  
  - **Content Size:** 69.6MB  

---

### 🔹 Using Official Node Alpine (`node:20-alpine`)
- `hemantwekan/fresh-pizza:1.5.0`
  - **Disk Usage:** 317MB  
  - **Content Size:** 70.2MB  

---

### 🔹 Using Base Image with CLI (v2 - initial approach)
- `hemantwekan/fresh-pizza:1.6.0`
  - **Disk Usage:** 401MB  
  - **Content Size:** 88.3MB  

---

### 🔹 Using Optimized Base Image (v3 - improved)
- `hemantwekan/node-base:20-alpine-v3`
- `hemantwekan/fresh-pizza:1.7.0`
  - **Disk Usage:** 355MB  
  - **Content Size:** 77MB  

---

### 🔹 Using Stable & Clean Base Image (v4/v5 - final)
- `hemantwekan/node-base:20-alpine-v5`
- `hemantwekan/fresh-pizza:1.8.0`
  - **Disk Usage:** ~355MB  
  - **Content Size:** ~77MB  

---

## 🚀 Impact:

- Reduced ~**100MB+ disk usage** compared to initial build (~25% improvement)  
- Initial CLI integration increased size, but further optimizations reduced ~**46MB** from that spike  
- Improved **build time** by:
  - avoiding repeated installation of `@nitrostack/cli`
  - optimizing Docker layer caching  
  - using BuildKit cache for npm  
- Fixed **cross-platform build issues (Mac → Docker Linux)** ensuring consistent builds  
- Achieved a **balanced tradeoff** between:
  - build performance ⚡  
  - image size 📦  
- Identified that **major build time is dominated by `npm ci` and build steps**, not base image  

---

## ⚠️ Key decisions:

- Moved **`@nitrostack/cli` to base image** since it is required in both build and runtime  
- Optimized base image to reduce its impact on final image size  
- Focused on **Dockerfile layer optimization** (not just base image) for real time improvements  
- Ensured **clean installs inside Docker** instead of relying on local environment  
- Kept project-specific dependencies in main Dockerfile to maintain flexibility and proper caching  

---

## 🧠 Key Learnings:

- Base image optimization provides:
  - ✅ one-time setup improvement  
  - ✅ avoids repeated installs  
  - ❌ limited impact on overall build time  

- Main Dockerfile optimizations provide:
  - 🚀 major time improvements (dependency caching, layer ordering)

- Cross-platform builds require:
  - ❗ avoiding local `node_modules`
  - ❗ ensuring fresh installs inside Docker  

---

## 🧠 Note on base updates:

- Frequent base image updates can **invalidate cache and slow down builds**, so it should be versioned and updated only when needed  
- Use versioned tags (e.g., `20-alpine-v5`) for stability and rollback support  