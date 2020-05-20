FROM node:alpine

#RUN groupadd -r user && useradd -r -g user user

WORKDIR /opt/frontend/
EXPOSE 4100

#COPY --chown=user:user . .
COPY . .

ARG BACKEND_URL_ARG
ENV BACKEND_URL=${BACKEND_URL_ARG:-"https://back.maxn.test.coherentprojects.net"}

RUN npm install; \
    sed -i~ "s|\(API_ROOT = \).*;|\1'$BACKEND_URL';|" src/agent.js

#USER user

CMD ["npm", "start"]