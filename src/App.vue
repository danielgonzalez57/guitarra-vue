<script setup>
    import {ref, reactive, onMounted, watch} from 'vue'
    import {db} from './data/guitarras'
    import Guitarra from './components/guitarra.vue'
    import Header from './components/header.vue'
    import Footer from './components/Footer.vue'

    const guitarras = ref([])
    const carrito = ref([])
    const guitarra = ref({})

    watch(carrito, () => {

    }, {
        deep: true
    })

    onMounted(() =>{
        guitarras.value = db;
        guitarra.value = db[3]

        const carritoStorage = localStorage.getItem('carrito')

        if(carritoStorage){
            // parse para convertirlo otra vez en arreglo y anadirlo al carrito de compras
            carrito.value = JSON.parse(carritoStorage)
        }
    })

    const guardarLocalStorage = () => {
        //convertir un arreglo carrito a string para almacenarlo en localstorage 
        localStorage.setItem('carrito', JSON.stringify(carrito.value))
    }

    const agregarCarrito = (guitarra) => {
        const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
        
        if(existeCarrito >= 0){
            carrito.value[existeCarrito].cantidad++
        } else {
            guitarra.cantidad = 1;
            carrito.value.push(guitarra);
        }

        guardarLocalStorage();
    }

    const decrementarCantidad = (id) =>{
        const index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad <= 1) return
        carrito.value[index].cantidad--
        guardarLocalStorage();
    }
    const incrementarCantidad = (id) =>{
        const index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad >= 5) return
        carrito.value[index].cantidad++
        guardarLocalStorage();
    }

    const eliminarProducto = (id) => {
        carrito.value = carrito.value.filter(producto => producto.id !== id)
        guardarLocalStorage();
    }
    const eliminarTodo = () => {
        carrito.value = []
        guardarLocalStorage();
    }

</script>

<template>
    <Header
        :carrito="carrito"
        :guitarra="guitarra"
        @incrementar-cantidad="incrementarCantidad"
        @decrementar-cantidad="decrementarCantidad"
        @agregar-carrito="agregarCarrito"
        @eliminar-producto="eliminarProducto"
        @eliminar-todo="eliminarTodo"
    />
    
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra 
                v-for="guitarra in guitarras"
                :guitarra="guitarra"
                @agregar-carrito="agregarCarrito"
            />
        </div>
    </main>

    <Footer/>
</template>

