<script>
    import {fade, fly} from 'svelte/transition';
    import {onMount} from "svelte";

    export let movieId = 0;
    export let title = 'Movie Title';
    export let description = 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed euismod, nulla sit amet aliquam lacinia, nisl nisl aliquam nisl, nec aliquam nisl nisl sit amet nisl.';
    export let imageUrl = 'https://graphicdesignjunction.com/wp-content/uploads/2012/10/large/movie+posters+1.jpg';
    export let isFavorite = false;
    export let rating = 4.5;
    export let year = 2020;

    let storageKey = 'favorites';

    onMount(()=>{
        // Check if the movie is already favorited in local storage
        if (typeof localStorage !== 'undefined') {
            const favorites = JSON.parse(localStorage.getItem(storageKey)) || {};
            isFavorite = favorites[movieId] || false;
        }
    })

    function toggleFavorite() {
        isFavorite = !isFavorite;

        // Store the updated favorite state in local storage
        if (typeof localStorage !== 'undefined') {
            const favorites = JSON.parse(localStorage.getItem(storageKey)) || {};
            favorites[movieId] = isFavorite;
            localStorage.setItem(storageKey, JSON.stringify(favorites));
        }
    }

    function getAllFavorites() {
        if (typeof localStorage !== 'undefined') {
            const favorites = JSON.parse(localStorage.getItem(storageKey)) || {};
            return Object.entries(favorites).filter(([_, isFavorite]) => isFavorite).map(([id]) => id);
        }
        return [];
    }
</script>


<div class="relative max-w-sm h-96 rounded overflow-hidden shadow-lg transform transition-all duration-500 ease-in-out hover:-translate-y-1"
     in:fly={{ y: 50, duration: 500 }} out:fade={{ duration: 300 }}>
    <div class="absolute inset-0 bg-cover bg-center transition-opacity duration-500 ease-in-out"
         style="background-image: url('{imageUrl}');" in:fade={{ duration: 500 }}></div>
    <div class="relative bg-black bg-opacity-60 p-6 h-full flex flex-col justify-between transition-opacity duration-500 ease-in-out"
         in:fade={{ duration: 500 }}>
        <div>
            <a href="/media?id={movieId}" class="font-bold text-xl mb-2 text-white"
                in:fly={{ y: -20, duration: 500, delay: 200 }}>{title}</a>
            <p class="text-gray-200 text-base" in:fly={{ y: 20, duration: 500, delay: 300 }}>
                {#if description.split(/\s+/).length > 40}
                    {description.split(/\s+/).slice(0, 40).join(' ') + '...'}
                {:else}
                    {description}
                {/if}
            </p>
        </div>
        <div class="flex justify-between items-center">
            <div class="flex items-center">
                {#each Array(Math.floor(rating)) as _, i}
                    <svg class="w-4 h-4 text-yellow-400 fill-current" xmlns="http://www.w3.org/2000/svg"
                         viewBox="0 0 20 20">
                        <path d="M10 15l-5.878 3.09 1.123-6.545L.489 6.91l6.572-.955L10 0l2.939 5.955 6.572.955-4.756 4.635 1.123 6.545z"/>
                    </svg>
                {/each}
                {#if rating - Math.floor(rating) >= 0.5}
                    <svg class="w-4 h-4 text-yellow-400 fill-current" xmlns="http://www.w3.org/2000/svg"
                         viewBox="0 0 20 20">
                        <path d="M10 12.294l3.708-3.625-5.169.752L10 4.275l-1.539 4.146-5.169-.752 3.708 3.625-1.108 4.544z"/>
                    </svg>
                {/if}
            </div>
            <div class="flex items-center">
                <p class="text-white font-semibold">{year}</p>
            </div>
            <button class="ml-4 focus:outline-none" on:click={toggleFavorite}>
                {#if isFavorite}
                    <svg class="w-6 h-6 text-red-500 fill-current" xmlns="http://www.w3.org/2000/svg"
                         viewBox="0 0 20 20">
                        <path d="M10 3.22l-.61-.6a5.5 5.5 0 00-7.78 7.77L10 18.78l8.39-8.4a5.5 5.5 0 00-7.78-7.77l-.61.61z"/>
                    </svg>
                {:else}
                    <svg class="w-6 h-6 text-white fill-current" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                        <path d="M10 3.22l-.61-.6a5.5 5.5 0 00-7.78 7.77L10 18.78l8.39-8.4a5.5 5.5 0 00-7.78-7.77l-.61.61z"/>
                    </svg>
                {/if}
            </button>
        </div>
    </div>
</div>