# -------------------- Dockerfile.v2 --------------------
# Étape 1 : build
FROM node:18-alpine AS builder
WORKDIR /node-app
COPY package*.json ./
RUN npm install --only=production && npm cache clean --force



# Étape 2 : runtime léger
FROM node:18-alpine
WORKDIR /node-app
COPY --from=builder /node-app/node_modules ./node_modules
COPY . .
RUN apk add --no-cache curl
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD curl -fs http://localhost:3000/api/health || exit 1


CMD ["node", "server.js"]

# -------------------- Dockerfile.v1 --------------------

# FROM node:18
# WORKDIR /node-app
# COPY package*.json ./
# RUN npm install
# COPY . .
# EXPOSE 3000
# CMD ["npm", "start"]

