<script setup>
import { ref, reactive, onMounted, watch} from 'vue'
import { db } from './data/guitarras' // Base de datos simulada
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'

const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

watch(carrito, () => {
    guardarLocalStorage();
}, {
    deep: true,
}) // Metodo de ciclo de vida

onMounted(() => { // Metodo de ciclo de vida
    /**
     * Método de ciclo de vida que se ejecuta después de que la función ha sido declarada.
     * Los métodos de ciclo de vida en Vue.js son funciones que se ejecutan en diferentes etapas del ciclo de vida de un componente.
     * Estos métodos permiten a los desarrolladores ejecutar código en momentos específicos, como cuando un componente se monta, actualiza o destruye.
     */
    // Simulamos una peticion a una base de datos
    guitarras.value = db;
    guitarra.value = db[3];

    const carritoStorage = localStorage.getItem('carrito');
    if (carritoStorage) {
        carrito.value = JSON.parse(carritoStorage);
    }
});

const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value));
}

const agregarCarrito = (guitarra) => {
    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id);
    if (existeCarrito !== -1) {
        carrito.value[existeCarrito].cantidad++;
        return;
    }
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
}

const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id);
    if (carrito.value[index].cantidad <= 1) return;
    carrito.value[index].cantidad--;
}

const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id);
    if (carrito.value[index].cantidad >= 15) return;
    carrito.value[index].cantidad++;
}

const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(producto => producto.id !== id); // filter() es un método que se utiliza para crear un nuevo array con todos los elementos que cumplan una condición.
}

const vaciarCarrito = () => {
    carrito.value = [];
}
</script>

<template>
    <Header 
        :carrito="carrito"
        :guitarra="guitarra"
        @decrementar-cantidad="decrementarCantidad"
        @incrementar-cantidad="incrementarCantidad"
        @agregar-carrito="agregarCarrito"
        @eliminar-producto="eliminarProducto"
        @vaciar-carrito="vaciarCarrito"
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

<style scoped>

</style>
