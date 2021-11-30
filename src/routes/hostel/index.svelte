<script context="module">
	export async function load({ session }) {
		const user = session.user;

		if (!user || user.role.name !== 'Student') {
			return {
				status: 302,
				redirect: `/login`
			};
		}

		const data = { user };
		data.hostels = await api.get('hostels', session.jwt);
		return { props: data };
	}
</script>

<script>
	import { session } from '$app/stores';
	import * as api from '$lib/api';
	export let user, hostels;
	let selectedBlock, selectedRoom;

	import { RingLoader } from 'svelte-loading-spinners';
	let isLoading = false;

	const freeHostels = hostels.filter((hostel) => !hostel.allocation);

	const requestAllocation = async () => {
		isLoading = true;
		const hostel = freeHostels.filter(
			(hostel) => hostel.blockNumber == selectedBlock && hostel.roomNumber == selectedRoom
		)[0];

		const date = new Date();

		const data = {
			hostel: hostel.id,
			allocatedTo: user.id,
			allocatedOn: date.toLocaleString(),
			status: 'vacant'
		};

		const response = await api.post('allocations', data, $session.jwt);

		if (!response.error) {
			allocations.push(response);
			// document.location.reload();
		}
		isLoading = false;
	};

	const vacate = () => {
		const response = api.del(`allocations/${user.allocation.id}`, $session.jwt);
		response.then((data) => {
			console.log(data);
			if (!data.error) location.reload();
		});
	};
</script>

<svelte:head>
	<title>Hostel</title>
</svelte:head>

{#if isLoading}
	<div class="h-full w-full flex justify-center">
		<div class="m-auto">
			<RingLoader size="5" color="#86d2f9" unit="em" duration="2s" />
		</div>
	</div>
{:else if !user.allocation}
	<form on:submit|preventDefault={requestAllocation} id="request-hostel-form">
		<div class="px-4 py-5 space-y-6">
			<h1 class="text-2xl  font-bold">Request Allocation</h1>

			<div class="form-control w-4/12">
				<label class="label">
					<span class="label-text">Block Number</span>
				</label>
				<select
					bind:value={selectedBlock}
					name="blockNumber"
					class="input input-primary input-bordered"
				>
					{#each [...new Set(freeHostels.map((item) => item.blockNumber))] as blockNumber}
						<option value={blockNumber}>{blockNumber}</option>
					{/each}
				</select>
			</div>

			<div class="form-control w-4/12">
				<label class="label">
					<span class="label-text">Room Number</span>
				</label>
				<select
					bind:value={selectedRoom}
					name="roomNumber"
					class="input input-primary input-bordered"
				>
					{#each freeHostels.filter((hostel) => hostel.blockNumber == selectedBlock) as hostel}
						<option value={hostel.roomNumber}>{hostel.roomNumber}</option>
					{/each}
				</select>
			</div>
			<button class="btn btn-primary" type="submit"> Submit </button>
			<p>
				Note: You have not requested for an allocation. Please fill and submit the above form to
				book a Hostel.
			</p>
		</div>
	</form>
{:else if user.allocation.status === 'vacant'}
	<div class="flex min-h-full">
		<div class="m-auto text-center">
			<p>Please be patient</p>
			<p>
				Your previous allocation request for block {user.allocation.hostel.blockNumber} room {user
					.allocation.hostel.roomNumber} is being processed!
			</p>
		</div>
	</div>
{:else if user.allocation.status === 'allocated'}
	<div class="flex min-h-full">
		<div class="m-auto text-center">
			<p class="mb-5">
				You are allocated block {user.allocation.hostel.blockNumber} room {user.allocation.hostel
					.roomNumber}!
			</p>
			<button on:click={vacate} class="btn btn-primary"> Vacate </button>
		</div>
	</div>
{/if}
