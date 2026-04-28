# ============================================
# Stage 1 : Build — Environnement de compilation
# ============================================
FROM node:20-alpine AS build
WORKDIR /app

# Copier les fichiers de dépendances
COPY package*.json ./
COPY tsconfig.json ./

# Installer TOUTES les dépendances (y compris dev pour compiler TypeScript)
RUN npm install

# Copier le code source TypeScript
COPY src/ ./src/

# Compiler TypeScript → JavaScript
RUN npm run build

# ============================================
# Stage 2 : Runtime — Image de production minimale
# ============================================
FROM node:20-alpine AS runtime
WORKDIR /app

# Copier uniquement package.json pour installer les dépendances de PRODUCTION
COPY package*.json ./
RUN npm ci --omit=dev
# Flag pour exclure devDependencies (syntaxe npm v9+)

# Copier uniquement les fichiers compilés depuis le stage "build"
# Syntaxe : COPY --from=[NOM_STAGE] [SOURCE] [DEST]
COPY --from=build /app/dist ./dist

# Utilisateur non-root pour la sécurité
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

EXPOSE 8080

ENV APP_ENV=production
ENV NODE_ENV=production

CMD ["node", "dist/index.js"]

#Commande console

# Builder l'image multi-stage
##docker build -t tp2-app:v1 .

# Comparer les tailles
##docker images | grep tp2

# tp2-naive v1 202 MB
# tp2-app v1 139 MB
# Question : quelle est la réduction de taille en % ?
# Calcul : (taille_naive - taille_multistage) / taille_naive * 100 = ≈ 31.19 %