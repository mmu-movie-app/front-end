<script>
    import "../app.css";
    import { onMount, onDestroy } from "svelte";

    let isSidebarOpen = false;
    let favorites = [];
    let storageKey = 'favorites';
    let intervalId;

    function getAllFavorites() {
        if (typeof localStorage !== 'undefined') {
            const storedFavorites = JSON.parse(localStorage.getItem(storageKey)) || {};
            return Object.entries(storedFavorites).filter(([_, isFavorite]) => isFavorite).map(([id]) => id);
        }
        return [];
    }

    async function get(id) {
        try {
            const response = await fetch(`http://localhost:8080/view/${id}`);
            if (response.ok) {
                const data = await response.json();
                return data;
            } else {
                throw new Error('Failed to fetch movie data');
            }
        } catch (error) {
            console.error('Error:', error);
            return null;
        }
    }

    async function fetchFavorites() {
        const favoriteIds = getAllFavorites();
        const favoritePromises = favoriteIds.map(id => get(id));
        const favoriteData = await Promise.all(favoritePromises);
        favorites = favoriteData.filter(movie => movie !== null);
    }

    onMount(() => {
        fetchFavorites();

        // Start the interval to check for updates every 3 seconds
        intervalId = setInterval(fetchFavorites, 3000);
    });

    onDestroy(() => {
        // Clear the interval when the component is destroyed
        clearInterval(intervalId);
    });

    function toggleSidebar() {
        isSidebarOpen = !isSidebarOpen;
    }
</script>

<nav class="bg-white py-4 shadow-md">
    <div class="container mx-auto flex items-center justify-between px-6">
        <button on:click={toggleSidebar} class="text-gray-600 focus:outline-none hover:text-gray-800">
            <svg class="h-6 w-6 fill-current" viewBox="0 0 24 24">
                <path d="M4 6h16v2H4zm0 5h16v2H4zm0 5h16v2H4z"/>
            </svg>
        </button>
        <a href="/" class="absolute right-44"><img src="https://i.ibb.co/zR3vF0n/Designer-removebg-preview.png" alt="Logo" class="h-20 right-44"></a>
    </div>
</nav>

<div class="flex h-screen bg-gray-100">
    <aside class="bg-white fixed left-0 top-0 bottom-0 w-80 p-6 transition-all duration-300 ease-in-out transform shadow-lg {isSidebarOpen ? 'translate-x-0' : '-translate-x-full'}">
        <div class="flex items-center justify-between mb-6">
            <h2 class="text-2xl font-bold">Favorites</h2>
            <button on:click={toggleSidebar} class="text-gray-600 focus:outline-none hover:text-gray-800">
                <svg class="h-6 w-6 fill-current" viewBox="0 0 24 24">
                    <path d="M4 6h16v2H4zm0 5h16v2H4zm0 5h16v2H4z"/>
                </svg>
            </button>
        </div>
        <ul class="space-y-4">
            {#each favorites as favorite}
                <li>
                    <a href="/media?id={favorite.id}" class="flex items-center rounded-lg px-4 py-3 transition-colors duration-200 hover:bg-gray-100">
                        <img src={"https://media.themoviedb.org/t/p/w600_and_h900_bestv2/" + favorite.posters[0].file_path} alt={favorite.title} class="w-10 h-10 object-cover rounded-full mr-4">
                        <span class="text-lg font-medium">{favorite.title}</span>
                    </a>
                </li>
            {/each}
        </ul>
    </aside>

    <main class="flex-1 ml-0 transition-all duration-300 ease-in-out {isSidebarOpen ? 'ml-80' : ''}">
        <div class="">
            <slot/>
        </div>
    </main>
</div>