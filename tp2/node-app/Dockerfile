# Argument de build pour la version de Node
ARG NODE_VERSION=18

FROM node:${NODE_VERSION}-alpine

WORKDIR /app

# Variables d'environnement
ENV APP_ENV=production
ENV PORT=3000

# Copier d'abord package.json pour optimiser le cache
COPY package.json .

# Installer les dépendances
RUN npm install

# Puis copier le reste du code
COPY app.js .

EXPOSE ${PORT}

CMD ["npm", "start"]
