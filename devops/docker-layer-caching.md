# Docker Layer Caching — Do It Right

**Date:** 2026-03-14
**Category:** devops

## What I Learned

Layer order in Dockerfile massively affects build speed. Copy `package.json` BEFORE source code so npm install is cached between builds.

## Bad (rebuilds node_modules every time)

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY . .                    # ← invalidates cache on ANY file change
RUN npm ci
CMD ["node", "src/index.js"]
```

## Good (npm install is cached unless package.json changes)

```dockerfile
FROM node:20-alpine
WORKDIR /app

# 1. Copy only dependency files first
COPY package*.json ./
RUN npm ci --only=production   # ← cached unless package.json changes

# 2. Copy source after
COPY src/ ./src/

CMD ["node", "src/index.js"]
```

## Result

- First build: 2 minutes
- Subsequent builds (code change only): ~8 seconds

## Rule of Thumb

> Put things that change least often at the top, most often at the bottom.
