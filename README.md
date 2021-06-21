# Vue-LifeLines

Female convict life events visualized in lifelines. Life events are taken from trial and transportation records, conduct registers, marriage 
and permission to marry registers, death registers, freedoms records, sickbay entries...

Data provided by Digital History Tasmania.

Visualisation made using Vue.js, Bootstrap and D3.js. Icons by Font-Awesome. 

#### Try it out here:

https://monalena.github.io/vue-lifelines/ 

Work in progress: So far anchors (indicating the voyage) and convict names in the visualisations are interactive. 'Enter Convict Id' is active. 

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Deploy to Github-Pages
After 'npm run build'  run
```$xslt
git add dist && git commit -m "New commit"
```
then
```
git subtree push --prefix dist origin gh-pages
```
should this not work (due to pull issues) run
```
npm run deploy-demo
```
instead. 

For new deployment remember:

* remove /dist from gitignore file
* change vue.config.js to project name
* when loading data get your paths right with process.env.BASE_URL

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
