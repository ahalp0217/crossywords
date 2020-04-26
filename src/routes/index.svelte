<script>
  import jQ from "jquery";

  const LEFT_ARROW = 37;
  const UP_ARROW = 38;
  const RIGHT_ARROW = 39;
  const DOWN_ARROW = 40;

  let puzzleData;
  let currentSquare;
  let currentOrientation;

  async function getPuzzleData() {
    const response = await fetch(
      "https://raw.githubusercontent.com/doshea/nyt_crosswords/master/2010/09/11.json",
      {
        headers: new Headers({
          Accept: "application/json"
        })
      }
    );
    const data = await response.json();
    puzzleData = data;
    console.log(data);
    return data;
  }
  puzzleData = getPuzzleData();

  function handleKeyDown(e) {
    let keyCode = e.keyCode;
    switch (keyCode) {
      case RIGHT_ARROW: {
        let i = parseInt(e.target.id);
        while (true) {
          let cell = document.getElementById(String(i + 1));
          if (cell.readOnly) {
            i++;
          } else {
            cell.focus();
            break;
          }
        }
        break;
      }
      case LEFT_ARROW: {
        let j = parseInt(e.target.id);
        while (true) {
          let cell = document.getElementById(String(j - 1));
          if (cell.readOnly) {
            j--;
          } else {
            cell.focus();
            break;
          }
        }
        break;
      }
      case UP_ARROW: {
        let j = parseInt(e.target.id);
        while (true) {
          let cell = document.getElementById(String(j - puzzleData.size.cols));
          if (cell.readOnly) {
            j = j - puzzleData.size.cols;
          } else {
            cell.focus();
            break;
          }
        }
        break;
      }
      case DOWN_ARROW: {
        let j = parseInt(e.target.id);
        while (true) {
          let cell = document.getElementById(String(j + puzzleData.size.cols));
          if (cell.readOnly) {
            j = j + puzzleData.size.cols;
          } else {
            cell.focus();
            break;
          }
        }
        break;
      }
      // code block
      default:
        document.getElementById(e.target.id).value = String.fromCharCode(
          keyCode
        );
    }
  }

  function initSquare(el) {
    if (el.id === "0") {
      el.focus();
    }
  }
</script>

<style>
  h1 {
    font-size: 2.8em;
    text-align: center;
    text-transform: uppercase;
    font-weight: 700;
    margin: 0 0 0.5em 0;
  }

  .puzzle-grid {
    margin: auto;
    width: 1000px;
  }

  .open-square {
    width: 34px;
    height: 34px;
    border: 0.5px solid #000;
    display: inline-block;
    text-align: center;
    text-transform: uppercase;
  }

  .block {
    width: 34px;
    height: 34px;
    border: 0.5px solid #000;
    background-color: #000;
    display: inline-block;
  }

  input {
    color: transparent; /* Hide blinking cursor */
    text-shadow: 0 0 0 #000;
    font-weight: bold;
    margin-right: -3px;
  }

  input:hover {
    cursor: pointer;
  }

  input:focus {
    background-color: steelblue;
  }

  .row-break {
    height: 1px;
  }
</style>

<svelte:head>
  <title>Sapper project template</title>
</svelte:head>

<h1>Crossywords</h1>
<div class="puzzle-grid">
  {#await puzzleData then data}
    {#each data.grid as square, i}
      {#if i % data.size.cols === 0 && i > 0}
        <div class="row-break" />
      {/if}
      {#if square !== '.'}
        <input
          type="text"
          data-row={parseInt(i / data.size.cols)}
          data-column={i % data.size.cols}
          class="open-square"
          id={i}
          maxlength="1"
          use:initSquare
          on:keydown={handleKeyDown}
          autocomplete="off" />
      {:else}
        <input
          type="text"
          data-row={parseInt(i / data.size.cols)}
          data-column={i % data.size.cols}
          id={i}
          class="block"
          disabled
          readonly />
      {/if}
    {/each}
  {/await}
</div>
