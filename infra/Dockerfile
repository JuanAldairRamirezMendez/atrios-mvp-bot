FROM n8nio/n8n:latest

USER root

# Railway inyecta $PORT — n8n escucha en 5678 por defecto
ENV N8N_PORT=5678
ENV N8N_PROTOCOL=https
ENV N8N_HOST=0.0.0.0
ENV WEBHOOK_URL=${RAILWAY_PUBLIC_DOMAIN}
ENV GENERIC_TIMEZONE=${GENERIC_TIMEZONE:-America/Lima}

# Volumen persistente de Railway montado en /home/node/.n8n
ENV N8N_USER_FOLDER=/home/node/.n8n

RUN mkdir -p /home/node/.n8n && chown -R node:node /home/node/.n8n

USER node

EXPOSE 5678

CMD ["n8n"]