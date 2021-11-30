<script context="module">
	export async function load({ session }) {
		const user = session.user;
		if (!user) {
			return {
				status: 302,
				redirect: `/login`
			};
		}
		return { props: { user } };
	}
</script>

<script>
	export let user;
</script>

<svelte:head>
	<title>Home</title>
</svelte:head>

<div class="flex flex-col items-center justify-center">
	<div class="text-center lg:text-left my-20">
		<h1 class="mb-5 text-5xl font-bold">HtlMgmt</h1>
		<p>Hello there {user.firstName}!</p>
		<p class="mb-5">
			HtlMgmt is a complete hostel management system for requesting, allocating and vacating
			hostels.
		</p>
		<div class="mb-10">
			<p>Checkout other products similar to this</p>
			<ul class="mx-5">
				<li><a href="https://lib-sys.vercel.app/" class="text-purple-300">Library management system</a></li>
				<li><a href="https://vid-onl.vercel.app/" class="text-purple-300">Online tutorials</a></li>
			</ul>
		</div>
	</div>
</div>
