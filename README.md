This project was created to test automation based on docker - Travis CI - AWS 

### Creating project
```
create-react-app docker-react
```
### Adding Docker file
## Dockerfile.dev
```
FROM node:alpine
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
CMD ["npm", "run", "start"]```
```
## Dockerfile
```
FROM node:alpine as builder
WORKDIR '/app'
COPY package.json .
RUN npm install
COPY . .
RUN npm run build

FROM nginx
EXPOSE 80
COPY --from=builder /app/build /usr/share/nginx/html
```
### Adding project to github repo
 - Create a public project in github
 - get the remote link
 - in your computer
    - chnage dir to project root dir
    - git init
    - git add .
    - git commit -m "initial commit"
    - git remote add origin https://github.com/<your project remote address>
    - git push origin master
### Adding your project to Travis CI
 - Sign in to https://travis-ci.org/ (you can use github credentials)
 - From profile/Setings sigh your project to be monitored by Travis-CI
 - create a .travis.yml file in your project root dir.
 - After you see green in travis CI you can continue
```
sudo: required
service:
  - docker

before_install:
  - docker build -t msaint/docker-react -f  Dockerfile.dev .

scripts:
  - docker run -e CI=true msaint/docker-react npm run test

deploy:
  edge: true
  provider: elasticbeanstalk
  region: "us-east-2"
  app: "docker-react"
  env: "DockerReact-env"
  bucket_name: "elasticbeanstalk-us-east-2-191643054305"
  bucket_path: "docker-react"
  on:
    branch: master
  access_key_id: $AWS_ACCESS_KEY
  secret_access_key: $AWS_SECRET_KEY
```
### Creating a Beanstalk application in AWS console
   - Create an app in Beanstalk

