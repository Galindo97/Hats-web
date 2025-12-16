
<script lang="ts">
	import '../app.css';
	import favicon from '$lib/assets/favicon.svg';
	import { onMount } from 'svelte';
	import { Search, Mail, User, ShoppingBag, Menu, X } from 'lucide-svelte';
	let logoAnimated = false;
	let showNavbar = false;
	let searchOpen = false;
	let mobileMenuOpen = false;

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
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>


<!-- Navbar Always Visible -->
<nav class="fixed top-0 left-0 right-0 z-40 bg-black/80 backdrop-blur-md border-b border-gray-800">
	<div class="flex justify-between items-center p-5">
		<!-- Logo -->
		<a href="/" aria-label="Inicio">
			<img src="/LOGOCHANTES-NEGRO-MR_3x-8_1000x.png" alt="Chantes Logo" class="h-10 w-auto" style="filter: brightness(0) invert(1);" />
		</a>
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
				   class="bg-white/10 backdrop-blur-md text-white px-4 py-2 rounded-full outline-none transition-all duration-300 w-48 opacity-100 hidden md:block"
			   />
		</div>
		<!-- Right Icons -->
		   <div class="flex items-center gap-6">
			   <button class="hover:scale-110 transition-transform text-white">
				   <Mail size={22} />
			   </button>
			   <button class="hover:scale-110 transition-transform text-white">
				   <User size={22} />
			   </button>
			   <button class="hover:scale-110 transition-transform text-white">
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
</nav>

<slot />
