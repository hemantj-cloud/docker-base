## ✅ What I did:

- Created a **base Docker image (`Dockerfile.base`)** with Node 20 Alpine and a non-root user for security  
- Installed **`@nitrostack/cli` globally in base image** to reuse across all projects and reduce build time  
- Optimized base image by:
  - Combining layers (apk + npm install)
  - Cleaning npm cache
  - Disabling audit/fund checks  
- Used this base image in the main **multi-stage Dockerfile** (builder + production stages)  
- Kept **npm install and build steps in the main Dockerfile** to handle project-specific dependencies  
- Optimized builds using **Docker cache (`--mount=type=cache`)** for faster npm installs  
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

## 🚀 Impact:

- Reduced ~**100MB+ disk usage** compared to initial build (~25% improvement)  
- Initial CLI integration increased size, but further optimizations reduced ~**46MB** from that spike  
- Improved **build time** by installing `@nitrostack/cli` once in base image instead of every build  
- Achieved a **balanced tradeoff** between:
  - build performance ⚡  
  - image size 📦  
- Maintained overall optimized and reusable Docker architecture  

---

## ⚠️ Key decisions:

- Moved **`@nitrostack/cli` to base image** since it is required in both build and runtime  
- Optimized base image to reduce its impact on final image size  
- Kept project-specific dependencies in main Dockerfile to maintain flexibility and proper caching   

---

## 🧠 Note on base updates:

- Frequent base image updates can **invalidate cache and slow down builds**, so it should be versioned and updated only when needed  
- Use versioned tags (e.g., `20-alpine-v3`) for stability and rollback support  