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
  let set = ["10", "11", "12", "13", "14", "15", "16"];
  let attackBonus = 5;
  let minDamage = 1;
  let maxDamage = 12;
  let critChance = 0.05;
  let critMultiplier = 2;
  let apr = 1;

  // average damage
  $: avgDamage = (minDamage + maxDamage) / 2;

  // n = (ac – attackBonus)
  $: n = ac.map((value) => parseInt(value) - attackBonus);

  // p = (20 – n)/20 + 0.05
  $: p = n.map((value) => {
    let v = (20 - value) / 20 + 0.05;
    return v;
  });

  // DPR = ((Hit% - Crit%) x dmg/hit + Crit% x dmg/crit) APR
  $: dpr = p.map((value) => {
    let v = (value - critChance) * avgDamage;
    let c = critChance * avgDamage * critMultiplier;
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
        <div class="min-w-full max-h-96">
          <Line data={dataset} {options} />
        </div>
      </div>
    </div>
  </div>
  <form class="divide-y divide-gray-100">
    <div class="grid grid-cols-1 gap-6 sm:grid-cols-3 lg:grid-cols-6 py-5">
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
          for="minDamage"
          class="block text-sm font-medium leading-6 text-gray-900"
          >Minimum Damage</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="minDamage"
            id="minDamage"
            min="0"
            max="20"
            bind:value={minDamage}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="min-damage"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="min-damage">
          The minimum amount of damage you deal per hit.
        </p>
      </div>
      <div>
        <label
          for="maxDamage"
          class="block text-sm font-medium leading-6 text-gray-900"
          >Max Damage</label
        >
        <div class="mt-2">
          <input
            type="number"
            name="maxDamage"
            id="maxDamage"
            min="0"
            max="20"
            bind:value={maxDamage}
            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6"
            aria-describedby="max-damage"
          />
        </div>
        <p class="mt-2 text-sm text-gray-500" id="max-damage">
          The maximum amount of damage you deal per hit.
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
    </div>
  </form>
</div>
