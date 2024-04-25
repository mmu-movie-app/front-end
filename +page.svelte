<script>
    import { onMount } from 'svelte';
    import { fade, fly } from 'svelte/transition';
    import MediaCard from '../components/media.svelte';
    import Filter from '../components/filter.svelte';

    let results = [];
    let isLoading = false;
    let error = null;

    async function handleSearch(event) {
        const variable = event.detail;
        console.log('Received variable:', variable);

        try {
            isLoading = true;
            const response = await fetch("http://localhost:8080/search", {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(variable),
            });

            if (response.ok) {
                const data = await response.json();
                console.log('Response data:', data);
                results = data;
            } else {
                error = `Error: ${response.status}`;
                console.error('Error:', response.status);
            }
        } catch (err) {
            error = 'An error occurred. Please try again later.';
            console.error('Error:', err);
        } finally {
            isLoading = false;
        }
    }

    onMount(() => {
        // Perform any necessary initialization or data fetching here
    });
</script>

<div class="container mx-auto px-4 py-8">

    <div class=" mb-8" in:fly="{{ y: -50, duration: 500 }}">
        <div class="justify-center pb-80">
            <img src="https://i.ibb.co/zR3vF0n/Designer-removebg-preview.png" alt="Logo" class="h-30 top-8 absolute left-1/2 transform -translate-x-1/2 pointer-events-none">
        </div>
        <Filter on:search={handleSearch} />
    </div>

    {#if isLoading}
        <div class="flex justify-center items-center mt-8">
            <div class="loader ease-linear rounded-full border-8 border-t-8 border-gray-200 h-32 w-32"></div>
        </div>
    {:else if error}
        <div class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded-lg mt-8" role="alert" transition:fade>
            <div class="flex items-center">
                <svg class="fill-current h-6 w-6 text-red-500 mr-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                    <path d="M2.93 17.07A10 10 0 1 1 17.07 2.93 10 10 0 0 1 2.93 17.07zm12.73-1.41A8 8 0 1 0 4.34 4.34a8 8 0 0 0 11.32 11.32zM9 11V9h2v6H9v-4zm0-6h2v2H9V5z"/>
                </svg>
                <div>
                    <p class="font-bold">Error</p>
                    <p class="text-sm">{error}</p>
                </div>
            </div>
        </div>
    {:else if results.length > 0}
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-8 mt-8">
            {#each results as data, index}
                <a  in:fly="{{ y: 50, duration: 500, delay: 100 * index }}">
                    <MediaCard
                            movieId={data.id}
                            title={data.title}
                            description={data.overview}
                            imageUrl={"https://media.themoviedb.org/t/p/w600_and_h900_bestv2/" + data.posters[0].file_path}
                            rating={data.rating}
                            year={data.release_year}
                    />
                </a>
            {/each}
        </div>
    {:else}
        <div class="text-center text-gray-500 mt-8" in:fade>
            <svg class="h-12 w-12 mx-auto mb-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6v6m0 0v6m0-6h6m-6 0H6"/>
            </svg>
            <p class="text-xl font-semibold">No results found</p>
            <p class="mt-2">Try adjusting your search or filter criteria.</p>
        </div>
    {/if}
</div>

<style>
    .loader {
        border-top-color: #3498db;
        animation: spin 1.5s linear infinite;
    }

    @keyframes spin {
        0% { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
    }
</style>