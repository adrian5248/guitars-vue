<script setup>
import { ref, computed, onMounted, watch } from 'vue';
import Footer from './components/Footer.vue';
import Guitarra from './components/Guitarra.vue';
import Header from './components/Header.vue';
import { db } from './data/guitarras';

const guitarras =  ref(db)
const carrito = ref([])

function guardaStorage(){
    localStorage.setItem('carrito',
    JSON.stringify(carrito.value)
    )
}


function recuperaStorage(){
    const storage = localStorage.getItem('carrito')
    return storage ? JSON.parse(storage): []
}


const total = computed(() => {
    return carrito.value
        .reduce((acc , guitar) => acc + guitar.precio * guitar.cantidad, 0)
})

function agregarCarrito(guitarra){
    //Validar id, si ya existe incrementa la cantidad
    const idCarrito = carrito.value.findIndex(g => g.id === guitarra.id)
    if (idCarrito === -1)
    carrito.value.push({ ...guitarra, cantidad: 1})
    else
    carrito.value[idCarrito].cantidad++
}

function agregaUno(id){
    const idCarrito = carrito.value.findIndex(g=> g.id === id)
    carrito.value[idCarrito].cantidad++
    
}

function quitaUno(id){
    const idCarrito = carrito.value.findIndex(g=> g.id === id)
    if(carrito.value[idCarrito].cantidad > 1)
        carrito.value[idCarrito].cantidad--

    
}

function quitaGuitarra(id){
    carrito.value = carrito.value
            .filter(guitar => guitar.id !== id)
}

function vaciarCarrito(){
    carrito.value =[] 
}


onMounted(() => {
    carrito.value = recuperaStorage()
})

watch(carrito, guardaStorage, {
    deep: true
})


</script>

    <template>
    <Header
        :carrito="carrito"
        :total="total"
        :guitar="guitarras[3]"
        @agregar-carrito="agregarCarrito"
        @agrega-uno="agregaUno"
        @quita-uno="quitaUno"
        @quita-guitarra="quitaGuitarra"
        @vaciar-carrito="vaciarCarrito"
    />
            <main class="container-xl mt-5">
                <h2 class="text-center">Nuestra Colección</h2>

                
                <div class="row mt-5">
                    <Guitarra
                        v-for="guitarra in guitarras"
                        :guitarra="guitarra"
                        :key="guitarra.id"
                        @agregar-carrito="agregarCarrito"
                        />
                </div>
            </main>
        <Footer/>
    </template>





