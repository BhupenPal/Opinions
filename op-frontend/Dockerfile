###################
# BUILD FOR LOCAL DEVELOPMENT
###################
FROM node:18-alpine As development

WORKDIR /usr/src/app

COPY --chown=node:node package.json yarn.lock ./

RUN yarn && mkdir .next

COPY --chown=node:node . .

USER root

RUN chmod 777 /usr/src/app/.next

USER node


###################
# BUILD FOR PRODUCTION
###################
FROM node:18-alpine As build

WORKDIR /usr/src/app

COPY --chown=node:node package.json yarn.lock ./

COPY --chown=node:node --from=development /usr/src/app/node_modules ./node_modules

COPY --chown=node:node . .

RUN yarn build

ENV NODE_ENV production

ENV NEXT_TELEMETRY_DISABLED 1

RUN yarn install --frozen-lockfile --production && yarn cache clean

USER node

###################
# PRODUCTION
###################
FROM node:18-alpine As production

COPY --from=build /app/package.json ./package.json
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app/.next ./.next
COPY --from=build /app/public ./public

CMD ["yarn", "start"]