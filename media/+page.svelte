<script>
    import { onMount } from 'svelte';
    import { fade, fly } from 'svelte/transition';
    import { quintOut } from 'svelte/easing';
    import MediaCard from '../../components/media.svelte'

    export let movie =
        {
            id: 1,
            title: "Resident Evil",
            overview: "When a virus leaks from a top-secret facility, turning all resident researchers into ravenous zombies and their lab animals into mutated hounds from hell, the government sends in an elite military task force to contain the outbreak.",
            genres: [

            ],
            cast: [

            ],
            crew: [

            ],
            backdrops: [

            ],
            posters: [

            ],
            budget: 33000000,
            revenue: 103000000,
            release_year: 2002,
            rating: 3.3085
        };

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

    async function loadMovie(id) {
        const fetchedMovie = await get(id);
        if (fetchedMovie) {
            movie = fetchedMovie;
        }
    }
    // Load the movie data when the component is mounted
    onMount(async () => {
        const urlParams = new URLSearchParams(window.location.search);
        $: if (urlParams) {
            const movieId = urlParams.get('id');
            if (movieId) {
                await loadMovie(movieId);
            }
        }
    });
</script>
<main
        style="background-image: linear-gradient(rgba(0, 0, 0, 0.8), rgba(0, 0, 0, 0.8)), url({movie.backdrops && movie.backdrops.length > 0 ? 'https://media.themoviedb.org/t/p/w1280'+movie.backdrops[0].file_path : '' });"
        class="bg-cover bg-center min-h-screen p-8 md:p-16 text-white w-full"
>
    <div class="max-w-7xl mx-auto">
        <div class="md:flex items-start mb-12" in:fly={{ y: 50, duration: 800, easing: quintOut }}>
            <div class="md:w-1/3 mb-8 md:mb-0 md:pr-12" in:fade={{ duration: 600 }}>
                <MediaCard
                        title={movie.title}
                        description={movie.overview}
                        imageUrl={movie.posters && movie.posters.length > 0 ? "https://media.themoviedb.org/t/p/w500" + movie.posters[0].file_path : ""}
                        rating={movie.rating}
                        year={movie.release_year}
                />
            </div>
            <div class="md:w-2/3">
                <div class="grid md:grid-cols-2 gap-12">
                    <div in:fly={{ x: -50, duration: 800, easing: quintOut }}>
                        <h2 class="text-4xl font-bold mb-6">Cast</h2>
                        <ul class="space-y-4 overflow-y-auto max-h-96">
                            {#if movie.cast}
                                {#each movie.cast as actor, i}
                                    <li class="flex items-center" in:fade={{ delay: i * 100 }}>
                                        <img src="{actor.profile_path ? 'https://media.themoviedb.org/t/p/w200'+actor.profile_path : 'https://via.placeholder.com/200x300'}" alt="{actor.name}" class="w-16 h-16 rounded-full object-cover mr-4">
                                        <div>
                                            <p class="text-xl font-semibold">{actor.name}</p>
                                            <p class="text-gray-400">{actor.character}</p>
                                        </div>
                                    </li>
                                {/each}
                            {/if}
                        </ul>
                    </div>
                    <div in:fly={{ x: 50, duration: 800, easing: quintOut }}>
                        <h2 class="text-4xl font-bold mb-6">Crew</h2>
                        <ul class="space-y-4 overflow-y-auto max-h-96">
                            {#if movie.crew}
                                {#each movie.crew as crew, i}
                                    <li class="flex items-center" in:fade={{ delay: i * 100 }}>
                                        <img src="{crew.profile_path ? 'https://media.themoviedb.org/t/p/w200'+crew.profile_path : 'https://via.placeholder.com/200x300'}" alt="{crew.name}" class="w-16 h-16 rounded-full object-cover mr-4">
                                        <div>
                                            <p class="text-xl font-semibold">{crew.name}</p>
                                            <p class="text-gray-400">{crew.job}</p>
                                        </div>
                                    </li>
                                {/each}
                            {/if}
                        </ul>
                    </div>
                </div>
                <div class="mt-12 flex justify-around" in:fly={{ y: 50, duration: 800, easing: quintOut }}>
                    <div class="text-center">
                        <p class="text-gray-400 uppercase tracking-wider mb-2">Budget</p>
                        <p class="text-4xl font-bold">${movie.budget.toLocaleString()}</p>
                    </div>
                    <div class="text-center">
                        <p class="text-gray-400 uppercase tracking-wider mb-2">Revenue</p>
                        <p class="text-4xl font-bold">${movie.revenue.toLocaleString()}</p>
                    </div>
                </div>
                <div class="mt-12" in:fly={{ y: 50, duration: 800, easing: quintOut }}>
                    <h2 class="text-4xl font-bold mb-6">Genres</h2>
                    <div class="flex flex-wrap">
                        {#if movie.genres}
                            {#each movie.genres as genre, i}
                                <span class="bg-blue-600 rounded-full px-6 py-3 text-xl font-semibold mr-4 mb-4" in:fade={{ delay: i * 100 }}>{genre.name}</span>
                            {/each}
                        {/if}
                    </div>
                </div>
            </div>
        </div>
    </div>
</main>