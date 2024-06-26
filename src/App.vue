<script setup>

import { ref, reactive, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Guitarra from "./components/Guitarra.vue";
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'


const guitarras = ref([])
const carrito = ref([])
const guitarra = ref({})

watch(carrito, () => {
    guardarLocalStorage()
}, {
    deep: true
})

onMounted(() => {
    guitarras.value = db
    guitarra.value = db[3]

    carrito.value = localStorage.getItem('carrito') ? JSON.parse(localStorage.getItem('carrito')) : []
})

const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

const agregarCarrito = (guitarra) => {

    const existeGuitarra = carrito.value.findIndex(producto => producto.id === guitarra.id)

    if (existeGuitarra < 0) {
        guitarra.cantidad = 1
        carrito.value.push(guitarra)
        return
    }

    if (carrito.value[existeGuitarra].cantidad >= 5) return
    carrito.value[existeGuitarra].cantidad++

}

const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if (carrito.value[index].cantidad < 5) {
        carrito.value[index].cantidad++;
    }
}

const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if (carrito.value[index].cantidad > 1) {
        carrito.value[index].cantidad--;
    }
}

const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id)
}

const vaciarCarrito = () => {
    carrito.value = []
}

</script>

<template>

    <Header :carrito="carrito" :guitarra="guitarra" @incrementar-cantidad="incrementarCantidad"
        @decrementar-cantidad="decrementarCantidad" @agregar-carrito="agregarCarrito"
        @eliminar-producto="eliminarProducto" @vaciar-carrito="vaciarCarrito" />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra v-for="guitarra in guitarras" :guitarra="guitarra" @agregar-carrito="agregarCarrito" />
        </div>
    </main>

    <Footer />


</template>
