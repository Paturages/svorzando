<script>
  let subdivisions = 4;
  let velocity = 1;
  let values = new Array(16).fill(1);
  let copied = null;

  const handleChange = $event => {
    const { value } = $event.target;
    if (Number.isNaN(+value)) return;
    const newValues = values.slice();
    newValues[$event.target.dataset.key] = +value;
    values = newValues;
  }

  const copy = $event => {
    navigator.clipboard.writeText(values[$event.target.dataset.key]);
    copied = $event.target.dataset.key;
  }

  const getVelocity = values => {
    let sum = 0;
    for (let i = 0; i < subdivisions; ++i) {
      sum += values[i] * (1/subdivisions);
    }
    return sum;
  }

  const normalize = () => {
    const currentVelocity = getVelocity(values);
    const newValues = values.slice();
    for (let i = 0; i < subdivisions; ++i) {
      newValues[i] = +(newValues[i] / (currentVelocity / velocity)).toFixed(4);
    }
    values = newValues;
  }

  const setValue = (index, value) => {
    const newValues = values.slice();
    newValues[index] = value;
    values = newValues;
  }

  const increment = $event => setValue($event.target.dataset.key, values[$event.target.dataset.key] + 1);
  const decrement = $event => setValue($event.target.dataset.key, values[$event.target.dataset.key] - 1);
  const resetValue = $event => setValue($event.target.dataset.key, 1);
</script>

<main>
  <div class="info">
    How to use:<br />
    Input your desired SV curve progression (linear, exponential, sine, make-up-your-own...), then hit normalize.<br />
    This assumes that subdivisions are the same step size, so keep that in mind while transcribing that on your map.<br />
    <br />
    Overall velocity:<br />
    {#each values.slice(0, subdivisions) as x, i}{#if i}+{/if} {x}×{(1/subdivisions).toFixed(4)}&nbsp;{/each}
    =
    <b>{getVelocity(values).toFixed(4)}</b>
  </div>
  <div class="container">
    {#each values.slice(0, subdivisions) as x, i}
      <div class="section">
        <input data-key={i} value={x} on:change={handleChange} />
        <div class="section-buttons">
          <button tabindex="-1" data-key={i} on:click={decrement}> - </button>
          <button tabindex="-1" data-key={i} on:click={copy}>{copied == i ? "✔️" : "📝"}</button>
          <button tabindex="-1" data-key={i} on:click={increment}> + </button>
          <br />
          <button tabindex="-1" data-key={i} on:click={resetValue}> 1 </button>
        </div>
      </div>
    {/each}
  </div>
  <div class="subdivisions">
    <div>Subdivisions</div>
    <input bind:value={subdivisions} type="range" min={2} max={16} step={1} />{subdivisions}
  </div>
  <div class="velocity">
    <div>Target velocity</div>
    <input bind:value={velocity} />
  </div>
  <div class="actions">
    <button on:click={normalize}>Normalize values</button>
  </div>
</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
      Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
    padding: 1em;
    margin: 0 auto;
  }

  .container {
    position: relative;
    display: flex;
    justify-content: center;
    gap: 1em;
    width: 1200px;
    margin: 1em 0 2em;
  }

  .section {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 5px;
  }

  .section-buttons {
    line-height: 2.5em;
    letter-spacing: 1.25em;
  }

  .section button {
    font-size: 1.25em;
    width: 2em;
  }

  .subdivisions, .velocity {
    display: flex;
    align-items: center;
    gap: 2em;
    padding: 1em 0;
  }

  .container input {
    text-align: center;
    font-size: 1.5em;
    padding: 0;
    width: 85%;
  }
</style>
