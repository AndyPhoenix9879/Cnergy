# CSCI3100 Group 16
CSCI3100 Software Engineering 

A group project that revolves around creating a centralised Club Web Application for CUHK, where students can easily discover and join
their desired club. 

The application is built using VueJS. 

NodeJS, npm and MySQL are assumed to be already installed on your system
## For Frontend

### Installing VueJS
```
npm install -g vue
```

### Installing the Vue CLI (needs this too)
```
npm install -g @vue/cli
```

### To check if installation was successful
```
vue --version
```

Some dependencies are required:

```
npm install -g typescript
```

```
npm install -g axios
```

```
npm install -g mysql
```

```
npm i vue-simple-alert
```

### Vuetify
```
vue add vuetify
```

### Element-UI:
```
npm i element-ui -S
```

## For Backend

```
npm install
```

### Configure the .env (or create it)
It should be in the root of the /server/ folder
```
JWT_SECRET=someTextCanBeAnything
```
### Setting up the database
```
mysql -u root -p

// or mysql, as long as you can get to the mysql terminal shell

CREATE DATABASE yourDatabase;

USE yourDatabase;
```

And you're set! 

Just exit that terminal, open two new ones under the /server/ directory:
First run:
```
npm run ts
```
...then:
```
npm run dev
```
Now you're backend is running, open the URL (with :3000) suggested by the terminal and you're done with Backend.

Once VueJS and the dependencies are installed and the repository is cloned onto your system, open up a Terminal and navigate towards the directory
holding the repository and type the following command:

```
cd client
npm run serve
```
