<script lang="ts">
import { page } from '$app/stores';
import { onMount } from 'svelte';
import { goto } from '$app/navigation';
import { ArrowLeft, ShoppingBag, Heart, Share2, Minus, Plus, Check, Truck, Shield, RotateCcw } from 'lucide-svelte';
import { get } from 'svelte/store';

// Datos de productos (puedes mover esto a un archivo aparte si lo prefieres)
type ProductImages = {
  [key: string]: string[];
};

const products = {
  cap1: {
    name: 'Gorra Classic White',
    price: 599,
    description: 'Diseño atemporal con materiales premium de la más alta calidad. Confeccionada con algodón 100% orgánico y ajuste perfecto para máxima comodidad.',
    colors: [
      { name: 'white', label: 'Blanco', hex: '#FFFFFF' },
      { name: 'black', label: 'Negro', hex: '#000000' },
      { name: 'gray', label: 'Gris', hex: '#6B7280' },
      { name: 'navy', label: 'Azul Marino', hex: '#1E3A8A' },
    ],
    sizes: ['S/M', 'L/XL', 'Ajustable'],
    images: {
      white: [
        '/whitehat1.webp',
        '/whitehat2.webp',
        '/whitehat3.webp',
        '/whitehat4.webp',
      ],
      black: [
        '/BlackHat1.webp',
        '/BlackHat2.webp',
        '/blackhat3.webp',
        '/blackhat4.webp',
      ],
      gray: [
        '/whitehat1.webp',
        '/whitehat3.webp',
        '/whitehat4.webp',
        '/whitehat2.webp',
      ],
      navy: [
        '/BlackHat1.webp',
        '/BlackHat2.webp',
        '/blackhat3.webp',
        '/blackhat4.webp',
      ],
    } as ProductImages,
    features: [
      { icon: Truck, text: 'Envío gratis en pedidos +$800' },
      { icon: Shield, text: 'Garantía de 6 meses' },
      { icon: RotateCcw, text: 'Devoluciones hasta 30 días' },
    ]
  },
  cap2: {
    name: 'Gorra Black Urban',
    price: 649,
    description: 'Estilo urbano con detalles minimalistas. Edición limitada. Material premium y ajuste cómodo.',
    colors: [
      { name: 'black', label: 'Negro', hex: '#000000' },
      { name: 'white', label: 'Blanco', hex: '#FFFFFF' },
      { name: 'gray', label: 'Gris', hex: '#6B7280' },
    ],
    sizes: ['Única', 'Ajustable'],
    images: {
      black: [
        '/BlackHat1.webp',
        '/BlackHat2.webp',
        '/blackhat3.webp',
        '/blackhat4.webp',
      ],
      white: [
        '/whitehat1.webp',
        '/whitehat2.webp',
        '/whitehat3.webp',
        '/whitehat4.webp',
      ],
      gray: [
        '/blackhat3.webp',
        '/blackhat4.webp',
        '/BlackHat1.webp',
        '/BlackHat2.webp',
      ],
    } as ProductImages,
    features: [
      { icon: Truck, text: 'Envío gratis en pedidos +$800' },
      { icon: Shield, text: 'Garantía de 6 meses' },
      { icon: RotateCcw, text: 'Devoluciones hasta 30 días' },
    ]
  }
};

let selectedSize: string = '';
let selectedColor: string = '';
let quantity: number = 1;
let currentImage: number = 0;
let liked: boolean = false;
let addedToCart: boolean = false;

type ProductKey = keyof typeof products;
type Product = (typeof products)[ProductKey];

$: productId = $page.params.id as ProductKey;
$: product = products[productId];
$: if (product && (!selectedColor || !product.colors.some(c => c.name === selectedColor))) {
  selectedColor = product.colors[0].name;
}
$: currentImages = product && selectedColor && product.images[selectedColor] ? product.images[selectedColor] : [];
$: total = product && quantity ? product.price * quantity : 0;

function handleAddToCart(): void {
  if (selectedSize) {
    addedToCart = true;
    setTimeout(() => (addedToCart = false), 2000);
  }
}

function goBack(): void {
  goto('/');
}
</script>

{#if product}
<div class="min-h-screen bg-black text-white">
  <!-- Header -->
  <header class="fixed top-0 left-0 right-0 z-50 bg-black/80 backdrop-blur-md border-b border-gray-800">
    <div class="max-w-7xl mx-auto px-6 py-4 flex items-center justify-between">
      <button class="flex items-center gap-2 hover:text-red-500 transition-colors" on:click={goBack}>
        <ArrowLeft size={20} />
        <span class="hidden sm:inline">Volver</span>
      </button>
      <div class="flex items-center">
        <a href="/" aria-label="Ir al inicio">
          <img src="/LOGOCHANTES-NEGRO-MR_3x-8_1000x.png" alt="Chantes Logo" class="h-10 w-auto" style="filter: brightness(0) invert(1);" />
        </a>
      </div>
      <button class="relative hover:scale-110 transition-transform">
        <ShoppingBag size={22} />
        {#if addedToCart}
          <span class="absolute -top-1 -right-1 w-5 h-5 bg-red-500 rounded-full flex items-center justify-center text-xs">
            {quantity}
          </span>
        {/if}
      </button>
    </div>
  </header>

  <!-- Main Content -->
  <div class="pt-24 pb-12 px-6">
    <div class="max-w-7xl mx-auto grid lg:grid-cols-2 gap-12 lg:gap-16">
      <!-- Image Gallery -->
      <div class="space-y-6">
        <!-- Main Image -->
        <div class="relative group">
          <div class="absolute -inset-1 bg-linear-to-r from-red-600 to-purple-600 rounded-3xl blur-xl opacity-25 group-hover:opacity-40 transition duration-700"></div>
          <div class="relative bg-gray-900 rounded-3xl overflow-hidden aspect-square">
            <img
              src={currentImages[currentImage]}
              alt={product.name}
              class="w-full h-full object-cover"
            />
            <!-- Image Navigation Dots -->
            <div class="absolute bottom-6 left-1/2 -translate-x-1/2 flex gap-2">
              {#each currentImages as _, index}
                <button
                  on:click={() => (currentImage = index)}
                  class="h-2 rounded-full transition-all duration-300 {currentImage === index ? 'w-8 bg-white' : 'w-2 bg-white/40'}"
                  aria-label={`Seleccionar imagen ${index + 1}`}
                ></button>
              {/each}
            </div>
          </div>
        </div>
        <!-- Thumbnail Gallery -->
        <div class="grid grid-cols-4 gap-3">
          {#each currentImages as img, index}
            <button
              on:click={() => (currentImage = index)}
              class="relative rounded-xl overflow-hidden aspect-square transition-all duration-300 {currentImage === index ? 'ring-2 ring-red-500 scale-105' : 'opacity-60 hover:opacity-100'}"
            >
              <img src={img} alt={`Vista ${index + 1}`} class="w-full h-full object-cover" />
            </button>
          {/each}
        </div>
      </div>
      <!-- Product Info -->
      <div class="space-y-8">
        <div>
          <h1 class="text-4xl md:text-5xl font-bold mb-4 tracking-tight">
            {product.name}
          </h1>
          <div class="flex items-center gap-4 mb-6">
            <span class="text-4xl font-bold text-red-500">
              ${product.price} <span class="text-2xl text-gray-400">MXN</span>
            </span>
            <span class="text-sm text-green-400 bg-green-400/10 px-3 py-1 rounded-full">
              En stock
            </span>
          </div>
          <p class="text-gray-400 text-lg leading-relaxed">
            {product.description}
          </p>
        </div>
        <!-- Color Selection -->
        <div>
          <label for="color-select" class="block text-sm font-semibold mb-3 text-gray-300">
            Color: <span class="text-white">{product.colors.find((c: { name: string }) => c.name === selectedColor)?.label}</span>
          </label>
          <div id="color-select" class="flex gap-3">
            {#each product.colors as color}
              <button
                on:click={() => (selectedColor = color.name)}
                class="relative w-12 h-12 rounded-full transition-all duration-300 {selectedColor === color.name ? 'ring-2 ring-red-500 ring-offset-2 ring-offset-black scale-110' : 'hover:scale-105'}"
                style="background-color: {color.hex}"
                title={color.label}
                aria-label={`Seleccionar color ${color.label}`}
              >
                {#if selectedColor === color.name}
                  <div class="absolute inset-0 flex items-center justify-center">
                    <Check size={20} class={color.name === 'white' ? 'text-black' : 'text-white'} />
                  </div>
                {/if}
              </button>
            {/each}
          </div>
        </div>
        <!-- Size Selection -->
        <div>
          <label for="size-select" class="block text-sm font-semibold mb-3 text-gray-300">
            Talla:
            {#if selectedSize}
              <span class="text-white">{selectedSize}</span>
            {/if}
          </label>
          <div id="size-select" class="grid grid-cols-3 gap-3">
            {#each product.sizes as size}
              <button
                on:click={() => (selectedSize = size)}
                class="py-3 px-4 rounded-xl font-medium transition-all duration-300 {selectedSize === size ? 'bg-red-500 text-white shadow-lg shadow-red-500/50' : 'bg-gray-800 text-gray-300'}"
                aria-label={`Seleccionar talla ${size}`}
              >
                {size}
              </button>
            {/each}
          </div>
        </div>
        <!-- Quantity -->
        <div>
          <label for="quantity-select" class="block text-sm font-semibold mb-3 text-gray-300">
            Cantidad
          </label>
          <div class="flex items-center gap-4" id="quantity-select">
            <div class="flex items-center bg-gray-800 rounded-xl overflow-hidden">
              <button
                on:click={() => (quantity = Math.max(1, quantity - 1))}
                class="p-3 transition-colors"
                aria-label="Disminuir cantidad"
              >
                <Minus size={20} />
              </button>
              <span class="px-6 font-semibold text-lg">{quantity}</span>
              <button
                on:click={() => (quantity = Math.min(10, quantity + 1))}
                class="p-3 transition-colors"
                aria-label="Aumentar cantidad"
              >
                <Plus size={20} />
              </button>
            </div>
            <div class="text-2xl font-bold">
              ${total} <span class="text-base text-gray-400">MXN</span>
            </div>
          </div>
        </div>
        <!-- Actions -->
        <div class="flex gap-3">
          <button
            on:click={handleAddToCart}
            disabled={!selectedSize}
            class="flex-1 py-4 px-8 rounded-full font-semibold text-lg transition-all duration-300 {selectedSize ? 'bg-linear-to-r from-red-500 to-pink-500 text-white shadow-lg hover:scale-105' : 'bg-gray-800 text-gray-500 cursor-not-allowed'}"
            style="border-radius: 9999px;"
          >
            {#if addedToCart}
              <span class="flex items-center justify-center gap-2">
                <Check size={20} /> Agregado
              </span>
            {:else}
              Agregar al carrito
            {/if}
          </button>
          <button
            on:click={() => (liked = !liked)}
            class="p-4 rounded-xl transition-all duration-300 {liked ? 'bg-red-500 text-white' : 'bg-gray-800'}"
          >
            <Heart size={24} fill={liked ? 'currentColor' : 'none'} />
          </button>
          <button class="p-4 rounded-xl bg-gray-800 transition-colors">
            <Share2 size={24} />
          </button>
        </div>
        {#if !selectedSize}
          <p class="text-sm text-red-400 -mt-4">
            * Por favor selecciona una talla
          </p>
        {/if}
        <!-- Features -->
        <div class="border-t border-gray-800 pt-8 space-y-4">
          {#each product.features as feature, index}
            <div class="flex items-center gap-3 text-gray-300">
              <div class="w-10 h-10 rounded-full bg-gray-800 flex items-center justify-center">
                <svelte:component this={feature.icon} size={20} class="text-red-500" />
              </div>
              <span>{feature.text}</span>
            </div>
          {/each}
        </div>
        <!-- Details -->
        <div class="border-t border-gray-800 pt-8">
          <h3 class="font-semibold text-lg mb-4">Detalles del producto</h3>
          <ul class="space-y-2 text-gray-400">
            <li>• Material: Algodón 100% orgánico</li>
            <li>• Ajuste: Correa ajustable en la parte trasera</li>
            <li>• Estilo: Snapback / 6 paneles</li>
            <li>• Cuidado: Lavar a mano con agua fría</li>
            <li>• Origen: Diseñado en México</li>
          </ul>
        </div>
      </div>
    </div>
    <!-- Related Products Preview -->
    <div class="max-w-7xl mx-auto mt-24">
      <h2 class="text-3xl font-bold mb-8 text-center">También te podría gustar</h2>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
        {#each [1,2,3] as item}
          <div class="group cursor-pointer">
            <div class="relative mb-4 overflow-hidden rounded-2xl bg-gray-900 aspect-square">
              <img 
                src={item === 1 ? '/BlackHat1.webp' : item === 2 ? '/whitehat1.webp' : '/blackhat3.webp'}
                alt="Producto relacionado"
                class="w-full h-full object-cover group-hover:scale-110 transition-transform duration-500"
              />
            </div>
            <h3 class="font-semibold mb-2">Gorra {item === 1 ? 'Urban Black' : item === 2 ? 'Sport Gray' : 'Classic Navy'}</h3>
            <p class="text-red-500 font-bold">${499 + (item * 50)} MXN</p>
          </div>
        {/each}
      </div>
    </div>
  </div>
</div>
{:else}
  <div class="text-center py-32 text-2xl text-red-500">Producto no encontrado</div>
{/if}
