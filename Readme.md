## ✅ What I did:

- Created a **base Docker image (`Dockerfile.base`)** with Node 20 Alpine and a non-root user for security  
- Built and pushed it as `hemantwekan/node-base:20-alpine` for reuse across services  
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

## 🚀 Impact:

- Reduced ~**100MB+ disk usage** compared to initial build (~25% improvement)  
- Slight increase (~4MB) when switching from custom base → official node alpine  
- Maintained overall optimized image size with cleaner and portable setup  

---

## ⚠️ Key decision:

- Did **not move npm install to base image** because dependencies are project-specific and it would break caching and flexibility  

---

## 🧠 Note on base updates:

- Frequent base image updates can **invalidate cache and slow down builds**, so it should be versioned and updated only when needed  