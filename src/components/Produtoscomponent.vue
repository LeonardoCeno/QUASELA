<template>

<div class="tudo" >
<div class="comercial" >
    <img src="./img/bannerbaguazera.png" alt="">
</div>
<div class="titulo" >
    <div class="linebranca" ></div>
<h3>PRINCIPAIS OBRAS</h3>
    <div class="linebranca" ></div>
</div>
<div class="soumdetalhe"></div>
<div class="produtos" >
    <div v-if="carregando" class="carregando">Carregando produtos...</div>
    <div v-else-if="erro" class="erro">{{ erro }}</div>
    <div class="lista" v-else>
        <div class="produto" v-for="produto in produtosVisiveisObras" :key="produto.id">
        <div class="nome-preco-imagem" style="position:relative;">
        <img :src="produto.image_path" alt="Imagem do produto" class="produto-imagem" />
        <img :src="produto.stock >= 1 ? DISPONIVELREAL : INDISPONIVELREAL" :alt="produto.stock >= 1 ? 'Disponível' : 'Indisponível'" class="disponivel-selo" />
        <h4>{{ produto.name }}</h4>
        <p>R$ {{ produto.price }}</p>
        </div>
        <div class="add">
        <button><img src="./img/maisumcarrinho.png" alt=""><p>Adicionar</p></button>
        <img src="./img/coraçaofav.png" alt="">
        </div>
    </div>
    </div>
    <div class="borda-mostrar" >
    <button v-if="produtos.length > 10" class="mostrar-mais" @click="alternarObras">
      {{ estadoBotaoObras === 'mais' ? 'Mostrar mais' : 'Mostrar menos' }}
    </button>
    </div>
</div>
<div class="comercial" >
    <img src="./img/bannerbaguazera3.png" alt="">
</div>
<div id="cord" class="titulo" >
    <div class="linebranca" ></div>
<h3>LIVROS</h3>
<div class="linebranca" ></div>
</div>
<div class="soumdetalhe"></div>
<div class="produtos" >
    <div v-if="carregando" class="carregando">Carregando produtos...</div>
    <div v-else-if="erro" class="erro">{{ erro }}</div>
    <div class="lista" v-else>
        <div class="produto" v-for="produto in produtosVisiveisOfertas" :key="produto.id">
        <div class="nome-preco-imagem" style="position:relative;">
        <img :src="produto.image_path" alt="Imagem do produto" class="produto-imagem" />
        <img :src="produto.stock >= 1 ? DISPONIVELREAL : INDISPONIVELREAL" :alt="produto.stock >= 1 ? 'Disponível' : 'Indisponível'" class="disponivel-selo" />
        <h4>{{ produto.name }}</h4>
        <p>R$ {{ produto.price }}</p>
        </div>
        <div class="add" >
        <button><img src="./img/maisumcarrinho.png" alt=""><p>Adicionar</p></button>
        <img src="./img/coraçaofav.png" alt="">
        </div>
    </div>
    </div>
    <div class="borda-mostrar">
    <button v-if="produtos.length > 10" class="mostrar-mais" @click="alternarOfertas">
      {{ estadoBotaoOfertas === 'mais' ? 'Mostrar mais' : 'Mostrar menos' }}
    </button>
    </div>
</div>
</div>

</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import api from '../services/api'

import DISPONIVELREAL from './img/DISPONIVELREAL.png'
import INDISPONIVELREAL from './img/INDISPONIVELREAL.png'

const apiBase = 'http://35.196.79.227:8000'
const produtos = ref([])
const carregando = ref(true)
const erro = ref(null)
const mostrarQuantidadeObras = ref(10)
const mostrarQuantidadeOfertas = ref(10)
const estadoBotaoObras = ref('mais') // 'mais' ou 'menos'
const estadoBotaoOfertas = ref('mais')

onMounted(async () => {
    try {
        const resposta = await api.get('/products/user/228')
        produtos.value = resposta.data.map(produto => ({
            ...produto,
            image_path: produto.image_path && !produto.image_path.startsWith('http')
                ? apiBase + produto.image_path
                : produto.image_path
        }))
    } catch (e) {
        erro.value = 'Erro ao carregar produtos'
    } finally {
        carregando.value = false
    }
})

const produtosVisiveisObras = computed(() => {
    return produtos.value.slice(0, mostrarQuantidadeObras.value)
})

const produtosVisiveisOfertas = computed(() => {
    return produtos.value.slice(0, mostrarQuantidadeOfertas.value)
})

function alternarObras() {
    if (estadoBotaoObras.value === 'mais') {
        mostrarQuantidadeObras.value = Math.min(produtos.value.length, mostrarQuantidadeObras.value + 10)
        estadoBotaoObras.value = 'menos'
    } else {
        mostrarQuantidadeObras.value = Math.max(10, mostrarQuantidadeObras.value - 10)
        if (mostrarQuantidadeObras.value === 10) {
            estadoBotaoObras.value = 'mais'
        }
    }
}

function alternarOfertas() {
    if (estadoBotaoOfertas.value === 'mais') {
        mostrarQuantidadeOfertas.value = Math.min(produtos.value.length, mostrarQuantidadeOfertas.value + 10)
        estadoBotaoOfertas.value = 'menos'
    } else {
        mostrarQuantidadeOfertas.value = Math.max(10, mostrarQuantidadeOfertas.value - 10)
        if (mostrarQuantidadeOfertas.value === 10) {
            estadoBotaoOfertas.value = 'mais'
        }
    }
}

</script>


<style scoped>

.borda-mostrar {
    display: flex;
    justify-content: center;
    position: relative;
    height: 50px;
    width: 159px;
    border: 1px solid #e4e4e4;
    border-top: 2px solid white;
    margin-bottom: 40px;
    z-index: 1000;
    bottom: 1px;
}

.borda-mostrar button {
    height: 40px;
    min-width: 135px;
    display: block;
    padding: 6px 18px;
    background-color: #010203ec;
    color: #e4e4e4;
    border-radius: 4px;
    font-size: 0.85rem;
    font-weight: bold;
    cursor: pointer;
}

.linebranca {
    width: 20vw;
    height: 1px;
    background-color: #ffffff;
}

.tudo {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
}

.titulo {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: 20px;
    background-color: #4f79a3;
    height: 4.5rem;
    width: 100%;
    max-width: 1250px;
    margin-bottom: 30px;
}

#cord {
    background-color: #3a9c73;
}

.titulo h3 {
    font-size: 26px;
    color: rgb(255, 255, 255);
}

.produtos {
    width: 100%;
    max-width: 1250px;
    margin: 0 auto;
}

.nome-preco-imagem {
    display: flex;
    flex-direction: column;
    align-items: center;
}

.nome-preco-imagem p {
    font-size: 22px;
    color: rgb(49, 49, 49);
    font-weight: bold;
}

.nome-preco-imagem img {
    margin-top: 10px;
    height: 225px;
    width: 160px;
    border: 0.1px solid rgb(212, 212, 212);
    filter: contrast(100%);
}

.nome-preco-imagem .disponivel-selo {
    width: 95px;
    height: auto;
    border: none;
}

.add {
    display: flex;
    flex-direction: row;
    width: 100%;
    align-items: center;
    justify-content: center;
    gap: 12px;
    opacity: 0;
    pointer-events: none;
    margin-top: 5px;
    margin-bottom: 10px;
    padding-bottom: 10px;
}

.add button {
    display: flex;
    align-items: center;
    background-color: #030a11f5;
    justify-content: center;
    padding: 8px;
    border-radius: 7px;
    gap: 7px;
    width: 150px;
}

.add button:hover {
    background-color: #02060ade;
}

.add button p {
    color: white;
}

.add button img {
    width: 20px;
    height: auto;
    border: none;
    filter: invert(1);
}

.add img {
    width: 20px;
    height: auto;
    filter: invert(6%) sepia(50%) saturate(200%) hue-rotate(160deg) brightness(100%) contrast(100%);
}

.add img:hover {
    opacity: 0.9;
}

.produto {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    text-align: center;
    width: 230px;
    height: 92%;
    margin-top: 3vh;
    padding-left: 10px;
    padding-right: 10px;
}

.produto:hover .add {
    opacity: 1;
    pointer-events: auto;
}

.produto:hover {
    box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.1);
}

.produto h4 {
    font-family: 'Roboto', sans-serif;
    font-size: 15px;
    color: rgb(65, 65, 65);
    margin-top: 10px;
    height: 40px;
}

.lista {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    justify-items: center;
    padding: 4px;
    position: relative;
    gap: 10px;
    border: 1px solid #ebebeb;
    border-top: none;
}

.mostrar-mais:hover {
    background-color: #02060ace;
    text-decoration: underline;
}

.soumdetalhe {
    width: 100%;
    max-width: 1250px;
    height: 1px;
    background-color: #888888;
    margin: 0 auto;
}

.comercial img {
    width: 100%;
    max-width: 1250px;
    height: auto;
    display: block;
    margin: 0 auto;
}

.disponivel-selo {
    position: absolute;
    left: 0px;
    bottom: 80px;
    width: 80px;
    height: auto;
    z-index: 2;
    border-radius: 9px;
}

@media (max-width: 1200px) {
    .lista {
        grid-template-columns: repeat(4, 1fr);
    }
}
@media (max-width: 950px) {
    .lista {
        grid-template-columns: repeat(3, 1fr);
    }
}
@media (max-width: 700px) {
    .lista {
        grid-template-columns: repeat(2, 1fr);
    }
    .titulo, .soumdetalhe, .comercial img, .produtos {
        max-width: 98vw;
    }
}
@media (max-width: 480px) {
    .lista {
        grid-template-columns: 1fr;
    }
    .produto {
        width: 95vw;
        min-width: 0;
        padding-left: 2vw;
        padding-right: 2vw;
    }
    .titulo, .soumdetalhe, .comercial img, .produtos {
        max-width: 100vw;
    }
}

</style>