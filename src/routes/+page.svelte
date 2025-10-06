<script>
  import { onMount } from "svelte";
  import { Card, Button, GradientButton } from "flowbite-svelte";
  const pairings = {
    Lindsay: "Denise",
    Alison: "Erika",
    Sophie: "Joann",
    "Marcus D": "Glenn",
    Nikki: "Sophie",
    Milt: "Dawn",
    Marsha: "Nikki",
    Opal: "Milt",
    Erika: "Marsha",
    Percy: "Alison",
    Joann: "Lindsay",
    Dawn: "Marcus D",
    Matt: "Timo",
    "Mark M": "Opal",
    Denise: "Percy",
    Glenn: "Mark M",
    Timo: "Matt",
  };

  onMount(() => {
    dadJoke();
  });
  const participants = Object.keys(pairings);
  let selectedName = $state();
  let secretSanta = $state();
  let generatedDadJoke = $state();

  const handleNameClick = (e) => {
    const participant = e.target.value;
    console.log("NAME HAS BEEN CLICKED");
    selectedName === participant
      ? (selectedName = undefined)
      : (selectedName = participant);
    console.log("selectedName", selectedName);
    secretSanta = undefined;
  };

  const dadJoke = async () => {
    const response = await fetch("https://icanhazdadjoke.com/", {
      headers: {
        Accept: "application/json",
      },
    });
    if (!response.ok) {
      console.log(`HTTP error! Status: ${response.status}`);
      generatedDadJoke = "Whoops! No joke found";
    }

    const data = await response.json();
    generatedDadJoke = data.joke;
  };

  const handleJokeClick = async () => {
    generatedDadJoke = undefined;
    await dadJoke();
  };
</script>

<div
  class="h-[100vh] w-full bg-slate-900 flex flex-col items-center justify-center gap-6"
>
  <Card
    class="p-4 max-w-3/4 rounded-lg border-none bg-slate-800 flex flex-col items-center gap-6"
  >
    <h1 class="text-center text-white text-2xl">
      It's Runbeck Family Secret Santa Time!
    </h1>
    <h3 class="text-white text-center text-lg">
      Please select your name from the list below to see your Secret Santa name:
    </h3>
    <div class="flex flex-wrap p-5 justify-center gap-3 w-5/6">
      {#each participants as participant}
        <button
          value={participant}
          onclick={handleNameClick}
          type="button"
          class="text-white bg-gradient-to-r from-cyan-400 via-cyan-500 to-cyan-600 hover:bg-gradient-to-br focus:ring-4 focus:outline-none focus:ring-cyan-300 dark:focus:ring-cyan-800 font-medium rounded-lg text-sm px-5 py-2.5 text-center me-2 mb-2"
          >{participant}</button
        >
      {/each}
    </div>
    <div class="flex justify-end">
      <GradientButton
        color="blue"
        onclick={() => (secretSanta = pairings[selectedName])}
        disabled={!selectedName}
        class="rounded-lg">Reveal!</GradientButton
      >
    </div>
    {#if secretSanta}
      <div class="text-white">
        Your Secret Santa is <strong>{secretSanta}!</strong>
      </div>
    {/if}
  </Card>

  <Card
    class="p-4 max-w-1/2 rounded-lg border-none bg-slate-800 flex flex-col items-center gap-3"
  >
    <h3 class="text-lg text-white text-center">
      Please enjoy this complimentary Dad joke:
    </h3>
    <p class="text-white text-center">{generatedDadJoke || "Loading ..."}</p>
    <GradientButton color="blue" onclick={() => dadJoke()}
      >Get New Joke</GradientButton
    >
  </Card>
</div>
