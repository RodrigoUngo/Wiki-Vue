[[_TOC_]]


#Integrar Bootstrap a Vue
Para utilizar los componentes de Bootstrap en el frontend de Vue, siga los pasos a continuación:

**Paso 1: Instalar bootstrap**
`npm install bootstrap-vue`

**Paso 2: Modificar el archivo main Frontend/src/main.js**

```js
import Vue from 'vue'
import App from './App'
import router from './router'
import BootstrapVue from 'bootstrap-vue' /*Colocar aqui la dependencia Bootstrap*/
import 'bootstrap/dist/css/bootstrap.css' /*importa archivos css*/
import 'bootstrap-vue/dist/bootstrap-vue.css' /*importa archivos css*/

Vue.config.productionTip = false
Vue.use(BootstrapVue); /*Colocar aqui el proyecto Vue*/

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})

```
[Referencias Bootstrap-vue](https://bootstrap-vue.org/)