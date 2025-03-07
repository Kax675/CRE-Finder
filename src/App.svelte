<script>
	import "fluent-svelte/theme.css";
	import {
		TextBox,
		Button,
		ContentDialog,
		ProgressRing,
		TextBlock,
		IconButton,
	} from "fluent-svelte";
	let loaderIsOpen = false;
	import Card from "./components/Card.svelte";
	let searchbar;
	let err1IsOpen = false;
	let err2IsOpen = false;
	let helpDialogIsOpen = true;
	function search() {
		if (searchbar !== "") {
			loaderIsOpen = true;
			fetch(
				`https://services.nvd.nist.gov/rest/json/cve/1.0/${searchbar}`
			)
				.then((response) => response.json())
				.then((data) => {
					loaderIsOpen = false;
					console.log(data);
					for (let i = 0; i < data.result.CVE_Items.length; i++) {
						console.log(data.result.CVE_data_timestamp);
						let card = new Card({
							target: document.getElementById("cards"),
							props: {
								severity:
									data.result.CVE_Items[i].impact.baseMetricV2
										.severity,
								vulnerabilityid:
									data.result.CVE_Items[i].cve.CVE_data_meta
										.ID,
								assigner:
									data.result.CVE_Items[i].cve.CVE_data_meta
										.ASSIGNER,
								datepub: `${data.result.CVE_Items[
									i
								].publishedDate.slice(0, 10)}`,
								datemod: `${data.result.CVE_Items[
									i
								].lastModifiedDate.slice(0, 10)}`,
								description:
									data.result.CVE_Items[0].cve.description
										.description_data[0].value,
								type: data.result.CVE_Items[i].cve.references
									.reference_data[0].tags[0],
								referencesrc:
									data.result.CVE_Items[i].cve.references
										.reference_data[0].url,
							},
						});
					}
				})
				.catch((error) => {
					err2IsOpen = true;
				});
		} else {
			err1IsOpen = true;
		}
	}
</script>

<div id="app-title">
	<IconButton on:click={() => (helpDialogIsOpen = true)}>
		<svg
			width="24"
			height="24"
			fill="none"
			viewBox="0 0 24 24"
			xmlns="http://www.w3.org/2000/svg"
			><path
				d="M4 4.5v15A2.5 2.5 0 0 0 6.5 22h13.25a.75.75 0 0 0 0-1.5H6.5a1 1 0 0 1-1-1h14.25a.75.75 0 0 0 .75-.75V4.5A2.5 2.5 0 0 0 18 2H6.5A2.5 2.5 0 0 0 4 4.5Zm7 3.518a.75.75 0 0 1-.75.732c-.75 0-.75-.751-.75-.751V7.99a1.403 1.403 0 0 1 .008-.134 2.222 2.222 0 0 1 .42-1.067c.454-.613 1.27-1.062 2.585-1.039.95.017 1.793.415 2.321 1.07.537.667.718 1.57.362 2.459-.362.905-1.181 1.265-1.652 1.471l-.05.023c-.28.123-.413.187-.493.251l-.001.001v.724a.75.75 0 0 1-1.5.001V11c0-.523.252-.897.563-1.147.25-.2.565-.338.786-.436l.038-.017c.542-.239.8-.387.917-.679a.92.92 0 0 0-.138-.96c-.222-.275-.629-.502-1.179-.511-.935-.016-1.245.285-1.353.432a.722.722 0 0 0-.134.33v.006Zm2.25 6.482a1 1 0 1 1-2 0 1 1 0 0 1 2 0Z"
				fill="var(--fds-text-primary)"
			/></svg
		>
	</IconButton>
</div>
<div id="search-bar">
	<TextBox
		bind:value={searchbar}
		placeholder="Search"
		type="search"
		on:search={search}
	/>
</div>

<div id="root">
	<div id="cards" />
</div>
<ContentDialog bind:open={loaderIsOpen}>
	<TextBlock variant="body">Please Wait...</TextBlock>
	<ProgressRing size={16} />
</ContentDialog>
<ContentDialog bind:open={err1IsOpen} title="Error">
	Please Enter A CVE ID
	<Button slot="footer" on:click={() => (err1IsOpen = false)}>Close</Button>
</ContentDialog>
<ContentDialog bind:open={err2IsOpen} title="Error">
	The ID you entered is invalid or the connection to the server cannot be
	established. Please enter a valid ID or check your internet
	<Button slot="footer" on:click={() => (err2IsOpen = false)}>Close</Button>
</ContentDialog>
<ContentDialog size={200} bind:open={helpDialogIsOpen} title="Welcome!">
	This is a tool to search for vulnerabilities in the CVE database. You can
	use the search bar to search for a CVE ID.
	<br />
	<br />
	The search bar will return a list of vulnerabilities that match your search.
	<br />
	You can click on a vulnerability to see more information about it.
	<br />
	<br />
	You can test the app by entering this example ID's:
	<div>
		<br />
		<TextBox value={"CVE-2020-36526"} readonly />
		<br />
		<TextBox value={"CVE-2020-36542"} readonly />
		<br />
		<TextBox value={"CVE-2022-30743"} readonly />
	</div>
	<br>
	You can visit the <Button variant="hyperlink" on:click={() => (window.open('https://cve.org'))}>https://cve.org</Button> for more information.

	<Button slot="footer" on:click={() => (helpDialogIsOpen = false)}
		>Close</Button
	>
</ContentDialog>

<style>
	#app-title {
		position: absolute;
		top: 16px;
		left: 16px;
	}
	#search-bar {
		position: absolute;
		width: 50%;
		height: auto;
		left: 50%;
		top: 16px;
		transform: translate(-50%, 0%);
	}
	#root {
		position: absolute;
		width: 100%;
		margin-top: 64px;
		height: calc(100% - 64px);
		height: auto;
	}
	#cards {
		display: flex;
		width: 75%;
		height: 100%;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		margin-left: 12.5%;
		gap: 16px;
	}
	:global(body) {
		background-color: var(--fds-solid-background-base);
		color: var(--fds-text-primary);
	}
</style>
