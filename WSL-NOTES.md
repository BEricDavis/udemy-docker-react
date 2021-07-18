Using the WSL2 terminal, start and run a container. It is very important that you do not use a PWD variable as shown in the lecture video as this will not work. Use the ~ alias for the home directory or type out the full path:
docker run -it -p 3000:3000 -v /app/node_modules -v ~/frontend:/app USERNAME:frontend
or
docker run -it -p 3000:3000 -v /app/node_modules -v /home/USER/frontend:/app USERNAME:frontend


# permissions and volumes: EACCES: permission denied, mkdir '/app/node_modules.cache'
There is an issue with permissions in regards to Linux hosts (which includes Windows WSL2) and volumes. It can be solved by doing the following:


    FROM node:alpine
     
    USER node
     
    RUN mkdir -p /home/node/app
    WORKDIR /home/node/app
     
    COPY --chown=node:node ./package.json ./
    RUN npm install
    COPY --chown=node:node ./ ./
     
    CMD ["npm", "start"]


Remember to update the working directory paths in your docker run command to /home/node/app instead of just /app

eg:

docker run -it -p 3000:3000 -v /home/node/app/node_modules -v ~/my-project-directory:/home/node/app IMAGE_ID

## This command worked: docker run -it -p 3000:3000 -v /home/node/app/node_modules -v ~/git-repo/frontend:/home/node/app -e CHOKIDAR_USEPOLLING=true bericdavis:frontend 

When we refactor to use Docker Compose, remember to update the working directory paths in your compose file:

volumes:

        volumes:
          - /home/node/app/node_modules
          - .:/home/node/app