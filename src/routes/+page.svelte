<script lang="ts">
	const grid: any = {};

	let turn = 'p1';

	for (let y = 0; y < 6; y++) {
		for (let x = 0; x < 6; x++) {
			grid[`${x}_${y}`] = '';
		}
	}

	function _boop([x, y]: number[], [vecX, vecY]: number[]) {
		const dirId = `${x + vecX}_${y + vecY}`;
		const dirDoubleId = `${x + vecX * 2}_${y + vecY * 2}`;

		if (grid[dirId] === undefined) {
			return;
		}

		if (grid[dirId] === '') {
			return;
		}

        if(grid[dirId].endsWith('-big') && !grid[`${x}_${y}`].endsWith('-big')) {
            return;
        }

		if (grid[dirDoubleId] !== '' && grid[dirDoubleId] !== undefined) {
			return;
		}

		if (grid[dirDoubleId] !== undefined) {
			grid[dirDoubleId] = grid[dirId];
		}

		grid[dirId] = '';
	}

	function _merge() {
		for (let y = 0; y < 6; y++) {
			for (let x = 0; x < 6; x++) {
				// x
				if (_isPair([`${x - 1}_${y}`, `${x}_${y}`, `${x + 1}_${y}`])) {
                    if(grid[`${x}_${y}`].endsWith('-big')) {
                        alert(`${grid[`${x}_${y}`]} Wins!`);
                        continue;
                    }

					grid[`${x}_${y}`] += '-big';
					grid[`${x - 1}_${y}`] = '';
					grid[`${x + 1}_${y}`] = '';
					continue;
				}

				// y
				if (_isPair([`${x}_${y - 1}`, `${x}_${y}`, `${x}_${y + 1}`])) {
                    if(grid[`${x}_${y}`].endsWith('-big')) {
                        alert(`${grid[`${x}_${y}`]} Wins!`);
                        continue;
                    }

					grid[`${x}_${y}`] += '-big';
					grid[`${x}_${y - 1}`] = '';
					grid[`${x}_${y + 1}`] = '';
					continue;
				}

				// /
				if (_isPair([`${x - 1}_${y + 1}`, `${x}_${y}`, `${x + 1}_${y - 1}`])) {
                    if(grid[`${x}_${y}`].endsWith('-big')) {
                        alert(`${grid[`${x}_${y}`]} Wins!`);
                        continue;
                    }
                    
					grid[`${x}_${y}`] += '-big';
					grid[`${x - 1}_${y + 1}`] = '';
					grid[`${x + 1}_${y - 1}`] = '';
					continue;
				}

				// \
				if (_isPair([`${x - 1}_${y - 1}`, `${x}_${y}`, `${x + 1}_${y + 1}`])) {
                    if(grid[`${x}_${y}`].endsWith('-big')) {
                        alert(`${grid[`${x}_${y}`]} Wins!`);
                        continue;
                    }

					grid[`${x}_${y}`] += '-big';
					grid[`${x - 1}_${y - 1}`] = '';
					grid[`${x + 1}_${y + 1}`] = '';
					continue;
				}
			}
		}
	}

	function _isPair(keys: string[]) {
		const spots = [];

		for (const key of keys) {
            if(grid[key] === undefined || grid[key] === '') {
                return false;
            }
            
			spots.push(grid[key]);
		}

		const spot1 = spots[0];
        console.log(spots);

		for (const spot of spots) {
			if (spot !== spot1) {
				return false;
			}
		}

		return true;
	}

	function onClick(id: string) {
		if (grid[id] !== '') {
			return;
		}

		grid[id] = turn;

		const x = +id.split('_')[0];
		const y = +id.split('_')[1];

		_boop([x, y], [0, -1]);
		_boop([x, y], [0, 1]);
		_boop([x, y], [-1, 0]);
		_boop([x, y], [1, 0]);

		_boop([x, y], [-1, -1]);
		_boop([x, y], [1, 1]);
		_boop([x, y], [-1, 1]);
		_boop([x, y], [1, -1]);

		_merge();

        _merge(); // check for win

		turn = turn === 'p1' ? 'p2' : 'p1';
	}
</script>

<div class="max-w-xl mx-auto p-3">
    <p
    class="text-center border border-black text-white mb-6 mt-3 rounded-md bg-neutral-800 text-neutral-500 p-4 text-3xl"
>
    {#if turn === 'p1'}
        <span class="font-bold text-green-700">Green Turn</span>
    {:else}
        <span class="font-bold text-red-700">Red Turn</span>
    {/if}
</p>

	<div class="grid-cols-12 w-full grid gap-2">
		{#each Object.entries(grid) as [pos, state], i (pos)}
			<button
				on:click={() => onClick(pos)}
				class="col-span-2 hover:bg-neutral-700 bg-neutral-800 rounded-md border border-black aspect-square flex items-center justify-center"
			>
				{#if state === 'p1'}
					<div class="rounded-full bg-green-600 border border-green-900 w-6 h-6" />
				{:else if state === 'p1-big'}
					<div class="rounded-full bg-green-600 border border-green-900 w-12 h-12" />
				{:else if state === 'p2'}
					<div class="rounded-full bg-red-600 border border-red-900 w-6 h-6" />
				{:else if state === 'p2-big'}
					<div class="rounded-full bg-red-600 border border-red-900 w-12 h-12" />
                    {:else}
                    <p class="text-neutral-600">{i+1}</p>
				{/if}
			</button>
		{/each}
	</div>


</div>
