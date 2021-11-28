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

		data.allocations = await api.get('allocations', session.jwt);

		return { props: data };
	}
</script>

<script>
	import { session } from '$app/stores';
	import * as api from '$lib/api';
	export let allocations, user;

	const vacancies = allocations.filter((allocation) => allocation.status === 'vacant');
	const allocated = allocations.filter((allocation) => allocation.status === 'allocated');

	const allocateHostel = (allocationId) => {
		const date = new Date();
		const data = {
			allocatedBy: user.id,
			allocatedOn: date.toLocaleString(),
			status: 'allocated'
		};
		const response = api.put('allocations/' + allocationId, data, $session.jwt);
		response.then((data) => {
			console.log(data);
			if (!data.error)
				location.reload();
		});
	};

	let openTab = 1;
	function toggleTabs(tabNumber) {
		openTab = tabNumber;
	}
</script>

<svelte:head>
	<title>Allocations</title>
</svelte:head>

<div class="container my-12 mx-auto px-4 md:px-12">
	<div class="flex flex-wrap -mx-1 lg:-mx-4 flex-col gap-y-10">
		<div class="tabs">
			<a class="tab tab-bordered {openTab === 1 ? 'tab-active' : ''}" on:click={() => toggleTabs(1)}
				>Pending Allocations</a
			>
			<a class="tab tab-bordered {openTab === 2 ? 'tab-active' : ''}" on:click={() => toggleTabs(2)}
				>Completed Allocations</a
			>
		</div>

		<div class={openTab === 1 ? 'block' : 'hidden'}>
			{#if vacancies.length === 0}
				<p>Yay! No pending Allocations found</p>
			{:else}
				<div class="overflow-x-auto">
					<table class="table w-full table-zebra">
						<thead>
							<tr>
								<th />
								<th>Block Number</th>
								<th>Room Number</th>
								<th>Requested On</th>
								<th>Allocate to</th>
								<th>Allocation</th>
							</tr>
						</thead>
						<tbody>
							{#each vacancies as vacancy, i}
								<tr>
									<th>{i + 1}</th>
									<td>{vacancy.hostel.blockNumber}</td>
									<td>{vacancy.hostel.roomNumber}</td>
									<td>{vacancy.allocatedOn}</td>
									<td>{vacancy.allocatedTo.username}</td>
									<td>
										<form on:submit={() => allocateHostel(vacancy.id)}>
											<button class="btn btn-primary" type="submit">
												Allocate
											</button>
										</form>
									</td>
								</tr>
							{/each}
						</tbody>
					</table>
				</div>
			{/if}
		</div>

		<div class={openTab === 2 ? 'block' : 'hidden'}>
			{#if allocated.length === 0}
				<p>No Allocations found</p>
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
							{#each allocated as allocation, i}
								<tr>
									<th>{i + 1}</th>
									<td>{allocation.hostel.blockNumber}</td>
									<td>{allocation.hostel.roomNumber}</td>
									<td>{allocation.allocatedBy.username}</td>
									<td>{allocation.allocatedTo.username}</td>
									<td>{allocation.allocatedOn}</td>
								</tr>
							{/each}
						</tbody>
					</table>
				</div>
			{/if}
		</div>
	</div>
</div>
