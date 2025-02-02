FROM node:18-alpine AS builder
RUN apk add --no-cache bash
WORKDIR /app
COPY package.json package-lock.json /app/
ENV CI=1
RUN npm ci --legacy-peer-deps
COPY nginx/ /app/nginx/
COPY assets/ /app/assets/
COPY locale/ /app/locale/
COPY scripts/ /app/scripts/
COPY src/ /app/src/
COPY vite.config.js /app/
COPY tsconfig.json /app/
COPY *.d.ts /app/
COPY schema.graphql /app/
COPY introspection.json /app/
COPY introspection*.json /app/
COPY .featureFlags/ /app/.featureFlags/
ARG API_URL
ENV API_URL=${API_URL:-http://localhost:8000/graphql/}
ARG APP_MOUNT_URI=/dashboard/
ENV APP_MOUNT_URI=${APP_MOUNT_URI}
ARG APPS_MARKETPLACE_API_URL
ENV APPS_MARKETPLACE_API_URL=${APPS_MARKETPLACE_API_URL:-https://apps.saleor.io/api/v2/saleor-apps}
ARG APPS_TUNNEL_URL_KEYWORDS
ENV APPS_TUNNEL_URL_KEYWORDS=${APPS_TUNNEL_URL_KEYWORDS}
ARG STATIC_URL=/dashboard/
ENV STATIC_URL=${STATIC_URL}
ARG SKIP_SOURCEMAPS=true
ENV SKIP_SOURCEMAPS=${SKIP_SOURCEMAPS}
ARG LOCALE_CODE=EN
ENV LOCALE_CODE=${LOCALE_CODE}
RUN npm run build

FROM nginx:stable-alpine AS runner
WORKDIR /app
COPY --from=builder /app/build/ /app/
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY ./nginx/replace-env-vars.sh /docker-entrypoint.d/50-replace-env-vars.sh