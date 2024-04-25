<script>
    import {createEventDispatcher} from 'svelte';
    import {slide} from 'svelte/transition';

    const filters_requiring_range = [2, 3, 5];
    const filters = [
        {id: 0, text: 'Title'},
        {id: 1, text: 'Genres'},
        {id: 2, text: 'Year'},
        {id: 3, text: 'Budget'},
        {id: 4, text: 'Actors'},
        {id: 5, text: 'Revenue'},
        {id: 6, text: 'Directors'}
    ];

    const State = {
        exact: 0,
        high: 1,
        low: 2
    };

    let applied_filters = [];
    let selected = "";
    let value = '';
    let filterState = State.exact;

    function GetSelectedId() {
        const selectedFilter = filters.find(filter => filter.text === selected);
        return selectedFilter ? selectedFilter.id : -1;
    }

    function ApplyFilter() {
        const selectedId = GetSelectedId();
        const existingFilterIndex = applied_filters.findIndex(filter => filter.filter_type === selectedId && filter.state === filterState);

        if (existingFilterIndex !== -1) {
            applied_filters[existingFilterIndex] = {filter_type: selectedId, value, state: filterState};
        } else {
            applied_filters = [...applied_filters, {filter_type: selectedId, value, state: filterState}];
        }

        selected = "";
        value = "";
        filterState = State.exact;
        handleSearch();
    }

    const dispatch = createEventDispatcher();

    function handleSearch() {
        dispatch('search', applied_filters);
    }

    function removeFilter(filterType, state) {
        applied_filters = applied_filters.filter(filter => !(filter.filter_type === filterType && filter.state === state));
        handleSearch();
    }
</script>

<div class="mb-8">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="bg-white shadow-md rounded-lg p-6">
            <div class="flex flex-col md:flex-row gap-4 items-end">
                <div class="flex-1">
                    <label htmlFor="filter-select" class="block text-sm font-medium text-gray-700">Select
                        Filter</label>
                    <select id="filter-select" bind:value={selected} on:change={() => { value = ''; }}
                            class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm">
                        <option value="" disabled selected>Choose a filter</option>
                        {#each filters as filter}
                            <option value={filter.text}>{filter.text}</option>
                        {/each}
                    </select>
                </div>
                {#key selected}
                    {#if filters_requiring_range.includes(GetSelectedId())}
                        <div class="flex-1">
                            <label for="filter-state" class="block text-sm font-medium text-gray-700">Filter
                                State</label>
                            <select id="filter-state" bind:value={filterState}
                                    class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm">
                                <option value={State.exact}>Exact</option>
                                <option value={State.low}>Less</option>
                                <option value={State.high}>Greater</option>
                            </select>
                        </div>
                    {/if}
                    <div class="flex-1">
                        <label for="filter-value" class="block text-sm font-medium text-gray-700">Value</label>
                        <input type="text" id="filter-value" bind:value={value}
                               class="mt-1 block w-full py-2 px-3 border border-gray-300 bg-white rounded-md shadow-sm focus:outline-none focus:ring-blue-500 focus:border-blue-500 sm:text-sm"
                               placeholder="Enter value">
                    </div>
                {/key}
            </div>
            <div class="mt-4">
                <button on:click={ApplyFilter}
                        class="w-full py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-blue-600 hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500">
                    Add Filter
                </button>
            </div>
        </div>
        {#if applied_filters.length > 0}
            <div class="bg-white shadow-md rounded-lg p-6 mt-8" transition:slide>
                <h3 class="text-lg font-bold mb-4">Applied Filters</h3>
                <div class="flex flex-wrap gap-2">
                    {#each applied_filters as filter}
                        <div class="flex items-center px-3 py-1.5 rounded-full text-sm font-medium"
                             class:bg-green-100={filter.state === State.exact}
                             class:bg-blue-100={filter.state === State.high}
                             class:bg-red-100={filter.state === State.low}>
                            <span class="mr-2">{filters.find(f => f.id === filter.filter_type).text}:</span>
                            <span class="mr-2">{filter.value}</span>
                            <button on:click={() => removeFilter(filter.filter_type, filter.state)}
                                    class="text-gray-400 hover:text-gray-500 focus:outline-none">
                                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"
                                     xmlns="http://www.w3.org/2000/svg">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                          d="M6 18L18 6M6 6l12 12"></path>
                                </svg>
                            </button>
                        </div>
                    {/each}
                </div>
            </div>
        {/if}
    </div>
</div>