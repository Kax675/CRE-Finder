<script>
    import "fluent-svelte/theme.css";
    import { TextBox, Button, TextBlock } from "fluent-svelte";
    let card;
    export let vulnerabilityid;
    export let severity;
    export let assigner;
    export let datepub;
    export let datemod;
    export let description;
    export let type;
    export let referencesrc;
    import { onMount } from "svelte";
    onMount(async () => {
        card.classList.add(`card-severity-${severity}`);
    });
</script>

<div bind:this={card} class="card">
<div id="normal">
 <TextBlock variant="title">{vulnerabilityid}</TextBlock>
 <TextBlock variant="body">Severity: <TextBlock style="margin-top: -6px" class="severity" variant="bodyStrong">{severity}</TextBlock></TextBlock>
</div>
<div id="expanded">
<TextBlock style="margin-bottom: 4px" variant="title">{vulnerabilityid}</TextBlock>
<TextBlock class="title" variant="body">Severity: <TextBlock class="severity" variant="bodyStrong">{severity}</TextBlock></TextBlock>
<TextBlock class="title" variant="body">Assigner: <TextBlock class=value variant="body">{assigner}</TextBlock></TextBlock>
<TextBlock class="title" variant="body">Published Date: <TextBlock class=value variant="body">{datepub}</TextBlock></TextBlock>
<TextBlock class="title" variant="body">Last Modified Date: <TextBlock class=value variant="body">{datemod}</TextBlock></TextBlock>
<TextBlock class="title" variant="body">Description: <TextBlock class=value variant="body">{description}</TextBlock></TextBlock>
<TextBlock class="title" variant="body">Type: <TextBlock class=value variant="body">{type}</TextBlock></TextBlock>
<TextBlock class="title" variant="body">Reference Source: <Button on:click={window.open(referencesrc)} variant="hyperlink">{referencesrc}</Button></TextBlock>

</div>
<div id="buttons">
<Button variant="standard" on:click={() => {
    card.classList.toggle("card-expanded");
}}>Details</Button>
<Button variant="accent">Save File</Button>
</div>
</div>