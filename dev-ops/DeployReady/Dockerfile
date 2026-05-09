FROM node:20-alpine

WORKDIR /app

COPY --chown=node:node app/package*.json ./
RUN npm install --only=production

COPY --chown=node:node app/ ./

USER node

ENV PORT=3000
EXPOSE 3000

CMD ["node", "index.js"]
