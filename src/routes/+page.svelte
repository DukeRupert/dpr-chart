<script lang="ts">
  import type { ChartOptions } from "chart.js";
  import { Line } from "svelte-chartjs";
  import {
    Chart as ChartJS,
    Title,
    Tooltip,
    Legend,
    LineElement,
    LinearScale,
    PointElement,
    CategoryScale,
  } from "chart.js";

  ChartJS.register(
    Title,
    Tooltip,
    Legend,
    LineElement,
    LinearScale,
    PointElement,
    CategoryScale
  );

  const title = "DPR Calculator";
  const introduction =
    "One of these easiest and most useful things to calculate in any tactical combat game is damage per round (DPR) on attack roles.";

  let ac = ["10", "11", "12", "13", "14", "15", "16"];
  let attackBonus = 5;
  let damageBonus = 1;
  let d4 = 0;
  let d6 = 0;
  let d8 = 0;
  let d10 = 0;
  let d12 = 0;
  let critChance = 0.05;
  let critMultiplier = 2;
  let apr = 1;

  /*To calculate the average dice value, 
  add 1 to the max value of all dice, 
  divide the result by 2, 
  then multiply that result by the 
  number of dies.*/
  function averageDamage(dice: number, count: number ): number {
    let base = (dice + 1) / 2
    return base * count
  }

  function totalAverageDamage(d4: number, d6:number, d8:number, d10:number, d12:number) {
    let avg_d4 = averageDamage(4, d4)
    let avg_d6 = averageDamage(6, d6)
    let avg_d8 = averageDamage(8, d8)
    let avg_d10 = averageDamage(10, d10)
    let avg_d12 = averageDamage(12, d12)
    return avg_d4 + avg_d6 + avg_d8 + avg_d10 + avg_d12
  }

  // average damage of dice
  $: totalAverageDiceDamage = totalAverageDamage(d4, d6, d8, d10, d12)

  // n = (ac – attackBonus)
  $: n = ac.map((value) => parseInt(value) - attackBonus);

  // p = (20 – n)/20 + 0.05
  $: p = n.map((value) => {
    let v = (20 - value) / 20 + 0.05;
    return v;
  });

  // DPR = ((Hit% - Crit%) x dmg/hit + Crit% x dmg/crit) APR
  $: dpr = p.map((value) => {
    let v = (value - critChance) * (totalAverageDiceDamage + damageBonus);
    let c = critChance * totalAverageDiceDamage * critMultiplier;
    let result = (v + c) * apr;
    return result.toString();
  });

  $: dataset = {
    labels: ac,
    datasets: [
      {
        label: "DPR",
        fill: true,
        lineTension: 0.3,
        backgroundColor: "rgba(225, 204,230, .3)",
        borderColor: "rgb(205, 130, 158)",
        borderCapStyle: "butt",
        borderDash: [],
        borderDashOffset: 0.0,
        borderJoinStyle: "miter",
        pointBorderColor: "rgb(205, 130,1 58)",
        pointBackgroundColor: "rgb(255, 255, 255)",
        pointBorderWidth: 10,
        pointHoverRadius: 5,
        pointHoverBackgroundColor: "rgb(0, 0, 0)",
        pointHoverBorderColor: "rgba(220, 220, 220,1)",
        pointHoverBorderWidth: 2,
        pointRadius: 1,
        pointHitRadius: 10,
        data: dpr,
      },
    ],
  };

  const options: ChartOptions = {
    responsive: true,
    scales: {
      x: {
        title: {
          display: true,
          text: `Armor Class`,
        },
      },
    },
  };
</script>

<div class="mx-auto max-w-4xl px-4 sm:px-6 lg:px-8 py-6">
  <div class="sm:flex sm:items-center">
    <div class="sm:flex-auto">
      <h1 class="text-base font-semibold leading-6 text-gray-900">{title}</h1>
      <p class="mt-2 text-sm text-gray-700">{introduction}</p>
    </div>
    <div class="mt-4 sm:ml-16 sm:mt-0 sm:flex-none">
      <!-- <button
          on:click={handleClick}
          type="button"
          class="block rounded-md bg-indigo-600 px-3 py-2 text-center text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
          >Add user</button
        > -->
    </div>
  </div>
  <div class="mt-8 flow-root">
    <div class="-mx-4 -my-2 overflow-x-auto sm:-mx-6 lg:-mx-8">
      <div class="inline-block min-w-full py-2 align-middle sm:px-6 lg:px-8">
        <div class="min-w-full max-h-96 h-80">
          <Line data={dataset} {options} />
        </div>
      </div>
    </div>
  </div>
  <form class="divide-y divide-gray-100">
    <div class="grid grid-cols-1 gap-6 sm:grid-cols-3 lg:grid-cols-6 py-5">
      <div>
        <label
          for="apr"
          class="block text-sm font-medium leading-6 text-gray-900">APR</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="apr"
            id="apr"
            min="0"
            max="20"
            bind:value={apr}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="attacks-per-round"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="attacks-per-round">
          How many attacks per round that you make.
        </p>
      </div>
      <div>
        <label
          for="attackBonus"
          class="block text-sm font-medium leading-6 text-gray-900"
          >Attack Bonus</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="attackBonus"
            id="attackBonus"
            min="0"
            max="20"
            bind:value={attackBonus}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="attack-bonus"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="attack-bonus">
          Your total bonus to attack rolls.
        </p>
      </div>
      <div>
        <label
          for="damageBonus"
          class="block text-sm font-medium leading-6 text-gray-900"
          >Damage Bonus</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="damageBonus"
            id="damageBonus"
            min="0"
            max="20"
            bind:value={damageBonus}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="damage-bonus"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="damage-bonus">
          Your ability bonus to damage.
        </p>
      </div>
      <div>
        <label
          for="d4"
          class="block text-sm font-medium leading-6 text-gray-900"
          >d4 Damage</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="d4"
            id="d4"
            min="0"
            max="20"
            bind:value={d4}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="d4-damage"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="d4-damage">
          The number of d4 damage dice.
        </p>
      </div>
      <div>
        <label
          for="d6"
          class="block text-sm font-medium leading-6 text-gray-900"
          >d6 Damage</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="d6"
            id="d6"
            min="0"
            max="20"
            bind:value={d6}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="d6-damage"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="d6-damage">
          The number of d6 damage dice.
        </p>
      </div>
      <div>
        <label
          for="d8"
          class="block text-sm font-medium leading-6 text-gray-900"
          >d8 Damage</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="d8"
            id="d8"
            min="0"
            max="20"
            bind:value={d8}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="d8-damage"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="d8-damage">
          The number of d8 damage dice.
        </p>
      </div>
      <div>
        <label
          for="d10"
          class="block text-sm font-medium leading-6 text-gray-900"
          >d10 Damage</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="d10"
            id="d10"
            min="0"
            max="20"
            bind:value={d10}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="d10-damage"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="d10damage">
          The number of d10 damage dice.
        </p>
      </div>
      <div>
        <label
          for="d12"
          class="block text-sm font-medium leading-6 text-gray-900"
          >d12 Damage</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="d12"
            id="d12"
            min="0"
            max="20"
            bind:value={d12}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="d12-damage"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="d12damage">
          The number of d12 damage dice.
        </p>
      </div>
      <div>
        <label
          for="critChance"
          class="block text-sm font-medium leading-6 text-gray-900"
          >Crit Chance</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="critChance"
            id="critChance"
            min="0"
            max="20"
            bind:value={critChance}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="crit-chance"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="crit-chance">
          Your chance to crit expressed as a decimal.
        </p>
      </div>
      <div>
        <label
          for="critMultiplier"
          class="block text-sm font-medium leading-6 text-gray-900"
          >Crit Multiplier</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="critMultiplier"
            id="critMultiplier"
            min="0"
            max="20"
            bind:value={critMultiplier}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="crit-multiplier"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="crit-multiplier">
          How much you multiply your damage on crit.
        </p>
      </div>
    </div>
  </form>
</div>
