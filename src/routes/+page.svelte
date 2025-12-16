<script lang="ts">
    // Logo y assets ahora se usan desde /static
  import { onMount } from 'svelte';
  import { Search, Mail, User, ChevronDown, ShoppingBag, Menu, X } from 'lucide-svelte';

  let logoAnimated = false;
  let showNavbar = false;
  let searchOpen = false;
  let mobileMenuOpen = false;
  let currentImages: Record<string, number> = { cap1: 0, cap2: 0 };

  // Las imágenes y videos se referencian por ruta absoluta desde /static

  const caps = [
    {
      id: 'cap1',
      name: 'Gorra Classic White',
      description: 'Diseño atemporal con materiales premium. Ideal para cualquier ocasión.',
      price: '$599 MXN',
      images: [
        '/whitehat1.webp',
        '/whitehat2.webp',
        '/whitehat3.webp',
        '/whitehat4.webp'
      ]
    },
    {
      id: 'cap2',
      name: 'Gorra Black Urban',
      description: 'Estilo urbano con detalles minimalistas. Edición limitada.',
      price: '$649 MXN',
      images: [
        '/BlackHat1.webp',
        '/BlackHat2.webp',
        '/blackhat3.webp',
        '/blackhat4.webp'
      ]
    }
  ];

  onMount(() => {
    if (sessionStorage.getItem('introShown')) {
      logoAnimated = true;
      showNavbar = true;
    } else {
      const timer = setTimeout(() => {
        logoAnimated = true;
        setTimeout(() => showNavbar = true, 500);
        sessionStorage.setItem('introShown', 'true');
      }, 2000);
      return () => clearTimeout(timer);
    }
  });

  onMount(() => {
    const intervals = caps.map(cap => {
      return setInterval(() => {
        currentImages = {
          ...currentImages,
          [cap.id]: (currentImages[cap.id] + 1) % cap.images.length
        };
      }, 3500);
    });
    return () => intervals.forEach(clearInterval);
  });

  function scrollToGallery() {
    const el = document.getElementById('product-gallery');
    if (el) el.scrollIntoView({ behavior: 'smooth' });
  }
</script>

<div class="bg-black text-white min-h-screen overflow-x-hidden">
  <!-- Logo Animation -->
  <div 
    class="fixed inset-0 z-50 flex items-center justify-center bg-black transition-all duration-1000 {logoAnimated ? 'h-20 bg-black/80 backdrop-blur-md' : 'h-screen'}"
    style="pointer-events: {logoAnimated ? 'none' : 'auto'}"
  >
    <div class="transition-all duration-1000 {logoAnimated ? 'h-12' : 'h-32'}">
      <div class="relative h-full flex items-center justify-center">
        <img src="/LOGOCHANTES-NEGRO-MR_3x-8_1000x.png" alt="Chantes Logo" class="h-full max-h-32 w-auto" style="filter: brightness(0) invert(1);" />
      </div>
    </div>
  </div>

  <!-- Navbar Icons -->
  <div class="fixed top-0 left-0 right-0 z-40 transition-opacity duration-500 {showNavbar ? 'opacity-100' : 'opacity-0'}">
    <div class="flex justify-between items-center p-5">
      <!-- Search -->
      <div class="flex items-center gap-3">
        <button 
          on:click={() => searchOpen = !searchOpen}
          class="text-white hover:scale-110 transition-transform"
        >
          <Search size={22} />
        </button>
        <input 
          type="text"
          placeholder="Buscar..."
          class="bg-white/10 backdrop-blur-md text-white px-4 py-2 rounded-full outline-none transition-all duration-300 {searchOpen ? 'w-48 opacity-100' : 'w-0 opacity-0'}"
        />
      </div>

      <!-- Right Icons -->
      <div class="hidden md:flex items-center gap-6">
        <button class="hover:scale-110 transition-transform">
          <Mail size={22} />
        </button>
        <button class="hover:scale-110 transition-transform">
          <User size={22} />
        </button>
        <button class="hover:scale-110 transition-transform">
          <ShoppingBag size={22} />
        </button>
      </div>

      <!-- Mobile Menu -->
      <button 
        class="md:hidden hover:scale-110 transition-transform"
        on:click={() => mobileMenuOpen = !mobileMenuOpen}
      >
        {#if mobileMenuOpen}
          <X size={24} />
        {:else}
          <Menu size={24} />
        {/if}
      </button>
    </div>
  </div>

  <!-- Mobile Menu Dropdown -->
  <div class="fixed top-20 right-0 bg-black/95 backdrop-blur-md z-40 w-64 transition-transform duration-300 {mobileMenuOpen ? 'translate-x-0' : 'translate-x-full'} md:hidden">
    <div class="flex flex-col gap-6 p-6">
      <button class="flex items-center gap-3 hover:text-red-500 transition-colors">
        <Mail size={20} />
        <span>Contacto</span>
      </button>
      <button class="flex items-center gap-3 hover:text-red-500 transition-colors">
        <User size={20} />
        <span>Iniciar sesión</span>
      </button>
      <button class="flex items-center gap-3 hover:text-red-500 transition-colors">
        <ShoppingBag size={20} />
        <span>Carrito</span>
      </button>
    </div>
  </div>

  <!-- Hero Section with Video -->
  <section class="relative w-full h-screen overflow-hidden">
    <div class="absolute inset-0 bg-linear-to-b from-black/60 via-black/40 to-black/80">
      <video class="absolute inset-0 w-full h-full object-cover opacity-50" src="/videoChantes.mp4" autoplay muted loop playsinline></video>
    </div>
    <div class="relative z-10 h-full flex flex-col items-center justify-end pb-32 px-6 text-center">
      <h1 class="text-5xl md:text-7xl font-bold mb-4 tracking-tight">
        Bienvenidos a <span class="text-red-500">Chantes</span>
      </h1>
      <p class="text-xl md:text-2xl text-gray-300 mb-8 max-w-2xl">
        Las gorras más exclusivas y de alta calidad
      </p>
      <button 
        on:click={scrollToGallery}
        class="animate-bounce hover:text-red-500 transition-colors"
      >
        <ChevronDown size={48} />
      </button>
    </div>
  </section>

  <!-- Product Gallery -->
  <section id="product-gallery" class="py-20 px-6 md:px-12 lg:px-24 space-y-32">
    {#each caps as cap, index}
      <div class="flex flex-col {index % 2 === 0 ? 'lg:flex-row-reverse' : 'lg:flex-row'} items-center justify-center gap-12 lg:gap-20">
        <!-- Image -->
        <div class="relative w-full max-w-lg group">
          <div class="absolute -inset-1 bg-linear-to-r from-red-600 to-purple-600 rounded-3xl blur opacity-25 group-hover:opacity-40 transition duration-1000"></div>
          <div class="relative overflow-hidden rounded-3xl bg-gray-900">
            {#each cap.images as img, imgIndex}
              <img
                src={img}
                alt={cap.name}
                class="w-full h-auto transition-opacity duration-700 {currentImages[cap.id] === imgIndex ? 'opacity-100' : 'opacity-0 absolute inset-0'}"
              />
            {/each}
          </div>
          <!-- Image Indicators -->
          <div class="absolute bottom-4 left-1/2 -translate-x-1/2 flex gap-2">
            {#each cap.images as _, imgIndex}
              <div
                class="h-2 rounded-full transition-all duration-300 {currentImages[cap.id] === imgIndex ? 'w-8 bg-white' : 'w-2 bg-white/40'}"
              ></div>
            {/each}
          </div>
        </div>
        <!-- Info -->
        <div class="max-w-lg text-center lg:text-left space-y-6">
          <h2 class="text-4xl md:text-5xl font-bold tracking-tight">
            {cap.name}
          </h2>
          <p class="text-lg text-gray-400 leading-relaxed">
            {cap.description}
          </p>
          <div class="text-3xl font-bold text-red-500">
            {cap.price}
          </div>
          <a href={`/product/${cap.id}`} class="group relative px-8 py-4 bg-red-500 rounded-full font-semibold text-lg overflow-hidden transition-all duration-300 hover:scale-105 hover:shadow-lg hover:shadow-red-500/50" style="border-radius:9999px;">
            <span class="relative z-10">Comprar ahora</span>
            <div class="absolute inset-0 bg-linear-to-r from-red-600 to-pink-600 opacity-0 group-hover:opacity-100 transition-opacity duration-300 rounded-full" style="border-radius:9999px;"></div>
          </a>
        </div>
      </div>
    {/each}
  </section>

  <!-- Footer -->
  <footer class="border-t border-gray-800 py-12 px-6 text-center">
    <div class="max-w-4xl mx-auto space-y-6">
      <div class="text-3xl font-bold tracking-wider">CHANTES</div>
      <p class="text-gray-400">
        Diseño exclusivo. Calidad premium. Estilo único.
      </p>
      <div class="flex justify-center gap-6 text-gray-400">
        <button class="hover:text-white transition-colors">Instagram</button>
        <button class="hover:text-white transition-colors">Facebook</button>
        <button class="hover:text-white transition-colors">Twitter</button>
      </div>
      <p class="text-sm text-gray-600 pt-6">
        © 2024 Chantes Brand. Todos los derechos reservados.
      </p>
    </div>
  </footer>
</div>