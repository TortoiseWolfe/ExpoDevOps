# base image
FROM node:14.17.6-stretch-slim as base

# set working directory
WORKDIR /usr/src/app

# install app dependencies
# COPY ["package.json", "package-lock.json*", "npm-shrinkwrap.json*", "./"]
# RUN npm i \
  # && npm cache clean --force

# install expo-cli
RUN npm install -g expo-cli \
  && npm cache clean --force

# run expo init
RUN npx create-expo-app --template tabs . \
# RUN expo init . \
  && npm cache clean --force

# add custom code
COPY . .
# COPY my-project/. .

# change permissions to node user
RUN chown -R node /usr/src

# BUILD (mid step to production)
FROM base as build
ENV PATH /usr/src/node_modules/.bin:$PATH
WORKDIR /usr/src/app
USER node
RUN npm run build
# end of build

# development environment
FROM base as dev
ENV NODE_ENV=development
ENV PATH /usr/src/node_modules/.bin:$PATH
ENV CI=true
WORKDIR /usr/src/app
RUN npm i \
  && mv node_modules ../ \
  && npm cache clean --force
USER node
CMD ["expo", "start"]

# test environment
FROM dev as test
ENV NODE_ENV=development
CMD ["expo", "test"]

# production environment
FROM base as prod
ENV NODE_ENV=production
# COPY --from=build /usr/src/app/build /usr/share/nginx/html
WORKDIR /usr/src/app
RUN npm i --only=production && npm cache clean --force
CMD ["expo", "start", "--no-dev", "--minify"]
