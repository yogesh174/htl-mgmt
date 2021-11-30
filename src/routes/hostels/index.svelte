<script context="module">
	export async function load({ session }) {
		const user = session.user;

		if (!user || user.role.name !== 'Admin') {
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
	import Icon from '@iconify/svelte';
	export let hostels;

	import { RingLoader } from 'svelte-loading-spinners';
	let isLoading = false;

	const addHostel = async () => {
		isLoading = true;
		const formElement = document.querySelector('form#add-hostel-form');
		const formElements = formElement.elements;
		const data = {};

		for (let i = 0; i < formElements.length; i++) {
			const currentElement = formElements[i];
			data[currentElement.name] = currentElement.value;
		}

		const response = await api.post('hostels', data, $session.jwt);
		if (!response.error) {
			hostels.push(response);
		}
		isLoading = false;
	};
</script>

<svelte:head>
	<title>Hostels</title>
</svelte:head>

{#if isLoading}
	<div class="h-full w-full flex justify-center">
		<div class="m-auto">
			<RingLoader size="5" color="#86d2f9" unit="em" duration="2s" />
		</div>
	</div>
{:else}
	<div class="container my-12 mx-auto px-4 md:px-12">
		<div class="flex flex-wrap -mx-1 lg:-mx-4 flex-col gap-y-10">
			{#if hostels.length === 0}
				<p>No hostels found</p>
			{:else}
				<div class="overflow-x-auto">
					<table class="table w-full table-zebra">
						<thead>
							<tr>
								<th />
								<th>Hostel block</th>
								<th>Hostel number</th>
								<th>Allocated to</th>
								<th>Allocated by</th>
								<th>Allocated on</th>
							</tr>
						</thead>
						<tbody>
							{#each hostels as hostel, i}
								<tr>
									<th>{i + 1}</th>
									<td>{hostel.blockNumber}</td>
									<td>{hostel.roomNumber}</td>
									{#if hostel.allocation && hostel.allocation.status === 'allocated'}
										<td>{hostel.allocation.allocatedBy.username}</td>
										<td>{hostel.allocation.allocatedTo.username}</td>
										<td>{hostel.allocation.allocatedOn}</td>
									{:else}
										<td>-</td>
										<td>-</td>
										<td>-</td>
									{/if}
								</tr>
							{/each}
						</tbody>
					</table>
				</div>
			{/if}
		</div>
	</div>

	<label
		for="my-modal-2"
		class="btn btn-primary btn-circle btn-md absolute bottom-2 right-2 modal-button"
	>
		<Icon icon="bi:plus-lg" width="25" height="25" />
	</label>

	<input type="checkbox" id="my-modal-2" class="modal-toggle" />

	<div class="modal">
		<div class="modal-box">
			<form on:submit|preventDefault={addHostel} id="add-hostel-form">
				<div class="px-4 py-5 space-y-6 sm:p-6">
					<div class="grid grid-cols-2 gap-4">
						<div class="form-control">
							<label class="label">
								<span class="label-text">Block Number</span>
							</label>
							<input
								type="number"
								placeholder="Hostel Block Number"
								name="blockNumber"
								class="input input-primary input-bordered"
							/>
						</div>

						<div class="form-control">
							<label class="label">
								<span class="label-text">Room Number</span>
							</label>
							<input
								type="number"
								placeholder="Hostel Room Number"
								name="roomNumber"
								class="input input-primary input-bordered"
							/>
						</div>
					</div>
				</div>

				<div class="modal-action">
					<label for="my-modal-2">
						<button for="my-modal-2" class="btn btn-primary" type="submit"> Add Hostel </button>
					</label>
					<label for="my-modal-2" class="btn">Close</label>
				</div>
			</form>
		</div>
	</div>
{/if}
