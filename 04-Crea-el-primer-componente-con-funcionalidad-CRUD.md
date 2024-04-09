[[_TOC_]]

#**Crea el nuevo componente**
En VS Code, dirijase a Frontend/src/components y cree una nueva carpeta con el nombre del proyecto [Book], posteriormente cree un nuevo archivo que tendrá el template [ListBook.vue] La ruta quedará de la siguiente manera:

**`frontend\src\components\Book\ListBook.Vue`**

![image.png](/.attachments/image-e54a697c-767c-45f0-8502-3a7e1d9ab55f.png)

Dentro del nuevo archivo, coloque el código a continuación:
```html
<template lang="html">

    <!-- Componente: Agregar el codigo HTML aqui -->
    <div class="container">
        <div class="row">
            <div class="col text-left">
                <h2>Listado de libros</h2>
            </div>
        </div>
    </div>


</template>
<script>
    export default {
        
    }
</script>

<style lang="css" scoped>
</style>        
```
**Definir la nueva URL deacceso para el nuevo componente**

Debe dirigirse a la carpeta router y abra el archivo index.js:

**`frontend\src\router\index.js`**

![image.png](/.attachments/image-cbaacb54-aa73-4be8-8bcf-cd5ce9bb5f0c.png)

luego importe el componente y defina la estructura:
```js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import ListBook from '@/components/Book/ListBook' /* Importar el nuevo componente */

Vue.use(Router)

export default new Router({
    routes: [
        {
            path: '/',
            name: 'HelloWorld',
            component: HelloWorld
        },
        {/*Agrega aquí la estructura del nuevo componente*/
            path: '/books',
            name: 'ListBook',
            component: ListBook
        }
    ],
    mode: 'history' /* Agregar esta línea para que muestre el nuevo componente */
})
```
**Modifica el archivo Frontend/src/App.vue**

Identificar la línea que contiene el logo de Vue y eliminarla. Esto evitara que se muestre el logo de Vue en cada componente:
```js
<template>
    <div id="app">
        <img src="./assets/logo.png"/> <!--- Quitar esta línea --->
    </div>
</template>

<script>
    export default {
        name: 'app'
    };
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
.....

```
Agrega una tabla en codigo HTML para que muestre el resultado de la consulta a la API desde el nuevo componente. utilice la siguiente ruta:
**`frontend\src\components\Book\ListBook.Vue`**

```html
<template lang="html">
    <!-- Componente: Agregar el codigo HTML aqui -->
    <div class="container">
        <div class="row">
            <div class="col text-left">
                <div class="">
                    <h2>Listado de libros</h2>
                    <b-button size="sm" :to="{ name: 'NewBook' }" variant="primary">Nuevo Libro</b-button><br>     
                </div>                    
                <!-- Obtener datos desde la API -->
                <div class="col-md-12">
                    <b-table striped hover :items="books" :fields="fields">
                        <!-- Columna con botones de Editar y Eliminar -->
                        <template slot="action" slot-scope="data">
                            <!-- este boton pasa el parámetro del registro que se va a editar -->
                            <b-button size="sm" variant="primary" :to="{ name: 'EditBook', params: { bookId: data.item.id } }">Editar</b-button>
                            <b-button size="sm" variant="danger" :to="{ name: 'DeleteBook', params: { bookId: data.item.id } }">Eliminar</b-button>
                        </template>
                    </b-table>
                </div>
            </div>
        </div>
    </div>
</template>
```
#**Obtener datos de la API y mostrarlos en el componente**

Reemplace el contenido que va dendro de la sección script del archivo: **`Frontend/src/components/Book/ListBook.vue`** 

El código a colocar se muestra a continuación:
```js
<script>
    import axios from 'axios';v /*Importa Axios*/
    export default {
        /*Define los datos que se van a mostrar en el componente*/
        data () {
            return {
                fields: [
                    { key: 'title', label: 'Titulo'},
                    { key: 'description', label: 'Descripción' },
                    { key: 'action', label: '' }
                ],
                books: []
            }
        },
        methods: { /*Funciones para obtener datos de la API*/
            getBooks() {
                const path = 'http://localhost:8000/api/v1.0/books'; /*Url de la API*/
                axios.get(path).then((response) => {
                    this.books = response.data /*Obtiene la respuesta de la API y la almacena en la variable*/                   
                })
                .catch((error) => { /*En caso de error, muestra la descripción del error*/
                    console.log(error)
                })
            }
        },
        created () { /*Ejecuta el metodo que obtiene la data*/
            this.getBooks()
        }
    }
</script>
<style lang="css" scoped>
</style>
```
Hasta este punto, la aplicación ya es capaz de mostrar la información que proviene de la API de Django en un componente de Vue.

SweetAlert es una librería que permite notificar mensajes a nuestro cliente de manera mas amigable. Escriba las siguiente instrucción en la línea de comandos para instalar SweetAlert:
**`npm install sweetalert`**

#**Cree una pantalla de actualización para el registro**

Cree un nuevo archivo con el Nombre [EditBook.vue] en el directorio de componentes. La ruta debe quedar de la siguiente manera: **`Frontend\src\components\Book\EditBook.vue`**

Agregue el código para el componente que editará los datos:
```Html
<template lang="html">
    <div class="container">
      <!-- Your component's template goes here -->
        <div class="row">
            <div class="col text-left">
                <h2>Editar Libro</h2>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <div class="card">
                    <div class="card-body">
                        <form @submit="onSubmit">
                            <!-- Your form field Title go here -->
                            <div class="form-group row">
                                <label for="title" class="col-sm-2 col-form-label">Título</label>
                                <div class="col-sm-">
                                    <input type="text" placeholder="Título" name="title" class="form-control" v-model.trim="form.title">
                                </div>
                            </div>
                            <!-- Your form field Description go here -->
                            <div class="form-group row">
                                <label for="description" class="col-sm-2 col-form-label">Descripción</label>
                                <div class="col-sm-">
                                    <textarea name="description" class="form-control" placeholder="Descripción" rows="3" v-model.trim="form.description"></textarea>
                                </div>
                            </div>
                            <!-- Your form buttons go here -->
                            <div class="rows">
                                <div class="col text-left">
                                    <b-button type="submit" variant="primary">Editar</b-button>
                                    <!-- Aqui se redirecciona a la lista de libros -->
                                    <b-button type="submit" class="btn-large-space" :to="{ name: 'ListBooks' }">Cancelar</b-button>
                                </div>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
  </template>
    
  <script>
  import axios from 'axios'
  import swal from 'sweetalert';
  export default {
    data() {
      return {
        // Aqui se obtiene el ID del libro
        bookId: this.$route.params.bookId
        form: {
          title: '',
          description: ''
        }
      },
      methods: {
        // Aqui se declara el evento que obtienen los datos del libro
        onSubmit(evt) {
            evt.preventDefault()
            //Aqui se actualiza el reistro hacia la base de datos.
            const path = `http://localhost:8000/api/v1.0/books${this.bookId}/`
            axios.put(path, this.form).then((response) => {
                this.form.title = response.data.title
                this.form.description = response.data.description  
                swal("Libro actualizado exitosamente", "", "success")
            })
            .catch((error) => {
                console.error(error)
            })
        },
        // Aqui conecta a la API para obtener los datos del libro
        getBook() {
            const path = `http://localhost:8000/api/v1.0/books${this.bookId}/`
            axios.get(path).then((response) => {
                this.form.title = response.data.title
                this.form.description = response.data.description                 
            })
            .catch((error) => {
                console.error(error)
            })
        }
      }
    },
    //Aqui se ejecuta el evento que obtienen los datos del libro
    created() {
      this.getBook()
    }
  }
  </script>  
  <style scoped>
  /* Your component's styles go here */
  </style>
```

Agregue el nuevo componente al archivo de rutas:
**`Frontend\src\router\index.js`**

Dentro del archivo, importe el nuevo componente
```js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import ListBook from '@/components/ListBook' /* Importar el nuevo componente para listar datos */
import EditBook from '@/components/EditBook' /* Importar el nuevo componente para editar datos*/

Vue.use(Router)

export default new Router({
    routes: [
        {
            path: '/',
            name: 'HelloWorld',
            component: HelloWorld
        },
        {/*Agrega aqui el nuevo componenete para listar datos*/
            path: '/books',
            name: 'ListBook',
            component: ListBook
        },
        {/*Agrega aqui el nuevo componenete para editar datos*/
            path: '/books/:bookId/edit',
            name: 'EditBook',
            component: EditBook
        }
    ],
    mode: 'history' /* Agregar esta linea para que muestre el nuevo componente */
})
```
#**Crear pantalla de eliminación de registros**

Cree un nuevo archivo con el nombre DeleteBook.vue en la siguiente ubicación:
**`Frontend\src\components\Book\DeleteBook.vue`**

Agregue las siguientes líneas de código:
```html
<template lang="html">
    <div class="container">
      <!-- Your component's template goes here -->
        <div class="row">
            <div class="col">
                <h3>¿Estás seguro que deseas eliminar este libro?</h3>
                <p>Título : {{ this.element.title }}</p>
                <p>Descripción : {{ this.element.description }}</p>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <b-button v-on:click="deleteBook" variant="danger">Eliminar</b-button>
            </div>
        </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios'
  import swal from 'sweetalert'
  export default {
    data() {
      return {
        bookId: this.$route.params.bookId,
        element: {
            title: '',
            description: ''
        }
      }
    },
    methods: {
        // Obtiene el ID del libro que se eliminará
        getBook() {
            const path = `http://localhost:8000/api/v1.0/books${this.bookId}/`
            axios.get(path).then((response) => {
                this.element.title = response.data.title
                this.element.description = response.data.description                 
            })
            .catch((error) => {
                console.error(error)
            })
        },
        // Elimina el registro del libro
        deleteBook() {
            const path = `http://localhost:8000/api/v1.0/books${this.bookId}/`
            axios.delete(path).then((response) => {
                location.href = '/books'
            })
            .catch((error) => {
                swal("No es posible eliminar el libro", "", "error")
            })
        }
    },
    //Ejecuta el evento que obtienen los datos del libro
    created() {
        this.getBook()
    }
}
  
  </script>
  
  <style scoped>
  /* Your component's styles go here */
  </style>
```
Crea una nueva ruta para el componente **DeleteBook.vue** en el archivo:
**`Frontend\src\router\index.js`**

```js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import ListBook from '@/components/ListBook' /* Importar el nuevo componente para listar datos */
import EditBook from '@/components/EditBook' /* Importar el nuevo componente para editar datos*/
import DeleteBook from '@/components/DeleteBook' /* Importar el nuevo componente para eliminar datos*/

Vue.use(Router)

export default new Router({
    routes: [
        {
            path: '/',
            name: 'HelloWorld',
            component: HelloWorld
        },
        {/*Agrega aqui el nuevo componenete para listar datos*/
            path: '/books',
            name: 'ListBook',
            component: ListBook
        },
        {/*Agrega aqui el nuevo componenete para editar datos*/
            path: '/books/:bookId/edit',
            name: 'EditBook',
            component: EditBook
        },
        {/*Agrega aqui el nuevo componenete para eliminar datos*/
        path: '/books/:bookId/delete',
        name: 'DeleteBook',
        component: DeleteBook
        }
    ],
    mode: 'history' /* Agregar esta linea para que muestre el nuevo componente */
})
```
# Crea pantalla para agregar registros
Para agregar nuevos registros es necesario crear un nuevo componente con el nombre **NewBook.vue** la ruta completa del archivo quedará de la siguiente manera:
**`Frontend\src\components\Book\NewBook.vue`**

Agregue las líneas de código:
```html
<template lang="html">
    <div class="container">
      <!-- Your component's template goes here -->
        <div class="row">
            <div class="col text-left">
                <h2>Nuevo Libro</h2>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <div class="card">
                    <div class="card-body">
                        <form @submit="onSubmit">
                            <!-- Your form field Title go here -->
                            <div class="form-group row">
                                <label for="title" class="col-sm-2 col-form-label">Título</label>
                                <div class="col-sm-">
                                    <input type="text" placeholder="Título" name="title" class="form-control" v-model.trim="form.title">
                                </div>
                            </div>
                            <!-- Your form field Description go here -->
                            <div class="form-group row">
                                <label for="description" class="col-sm-2 col-form-label">Descripción</label>
                                <div class="col-sm-">
                                    <textarea name="description" class="form-control" placeholder="Descripción" rows="3" v-model.trim="form.description"></textarea>
                                </div>
                            </div>
                            <!-- Your form buttons go here -->
                            <div class="rows">
                                <div class="col text-left">
                                    <b-button type="submit" variant="primary">Crear</b-button>
                                    <!-- Aqui se redirecciona a la lista de libros -->
                                    <b-button type="submit" class="btn-large-space" :to="{ name: 'ListBooks' }">Cancelar</b-button>
                                </div>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
  </template>
    
  <script>
  import axios from 'axios'
  import swal from 'sweetalert';
  export default {
    data() {
      return {
        // Aqui se obtiene el ID del libro
        form: {
          title: '',
          description: ''
        }
      },
      methods: {
        // Aqui se declara el evento que obtienen los datos del libro
        onSubmit(evt) {
            evt.preventDefault()
            //Aqui se crea el registro hacia la base de datos.
            const path = `http://localhost:8000/api/v1.0/books/`
            axios.post(path, this.form).then((response) => {
                this.form.title = response.data.title
                this.form.description = response.data.description  
                swal("Libro creado exitosamente", "", "success")
            })
            .catch((error) => {
                swal("No es posible crear el libro", "", "error")
            })
        }        
      }
    },
    //Aqui se ejecuta el evento que obtienen los datos del libro
    created() {
      
    }
  }
  </script>  
  <style scoped>
  /* Your component's styles go here */
  </style>
```
Se crea la nueva ruta para el componente: **NewBook.vue**; utilice la ruta:
**`Frontend\src\router\idex.js`**

```js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import ListBook from '@/components/ListBook' /* Importar el nuevo componente para listar datos */
import EditBook from '@/components/EditBook' /* Importar el nuevo componente para editar datos*/
import DeleteBook from '@/components/DeleteBook' /* Importar el nuevo componente para eliminar datos*/
import CreateBook from '@/components/CreateBook' /* Importar el nuevo componente para Crear datos*/

Vue.use(Router)

export default new Router({
    routes: [
        {
            path: '/',
            name: 'HelloWorld',
            component: HelloWorld
        },
        {/*Agrega aqui el nuevo componenete para listar datos*/
            path: '/books',
            name: 'ListBook',
            component: ListBook
        },
        {/*Agrega aqui el nuevo componenete para editar datos*/
            path: '/books/:bookId/edit',
            name: 'EditBook',
            component: EditBook
        },
        {/*Agrega aqui el nuevo componenete para eliminar datos*/
        path: '/books/:bookId/eliminar',
        name: 'DeleteBook',
        component: DeleteBook
        },
        {/*Agrega aqui el nuevo componenete para Crear datos*/
            path: '/books/new',
            name: 'CreateBook',
            component: CreateBook
        }
    ],
    mode: 'history' /* Agregar esta linea para que muestre el nuevo componente */
})
```

#Ejecutar el servidor backend Django y el servidor frontend Vue

Ejecute las siguientes instrucciones en la ventana de comando para activar el servidor web backend Django:
**`python manage.py runserver`**

Ejecute las siguientes instrucciones en la ventana de comando para activar el servidor web Frontend Vue:
**`npm run dev`**


[Referencias Bootstrap-vue](https://bootstrap-vue.org/)