<script>
  import { onMount } from "svelte";
  import ChromaPicker from "svelte-chroma-picker";

  let arr = [];
  let pointers = [];

  function* mergeSort() {
    function* merge(l, m, r) {
      const buf = [];
      let i1 = l;
      let i2 = m + 1;
      while (i1 <= m && i2 <= r) {
        if (arr[i1] < arr[i2]) {
          pointers = [
            {
              index: l,
              color: "#00ff00",
            },
            {
              index: m,
              color: "#00ffff",
            },
            {
              index: r,
              color: "#00ff00",
            },
            {
              index: i1,
              color: "#ff0000",
            },
          ];
          yield;
          buf.push(arr[i1++]);
        } else {
          pointers = [
            {
              index: l,
              color: "#00ff00",
            },
            {
              index: m,
              color: "#00ffff",
            },
            {
              index: r,
              color: "#00ff00",
            },
            {
              index: i2,
              color: "#ff0000",
            },
          ];
          yield;
          buf.push(arr[i2++]);
        }
      }
      while (i1 <= m) {
        pointers = [
          {
            index: l,
            color: "#00ff00",
          },
          {
            index: m,
            color: "#00ffff",
          },
          {
            index: r,
            color: "#00ff00",
          },
          {
            index: i1,
            color: "#ff0000",
          },
        ];
        yield;
        buf.push(arr[i1++]);
      }
      while (i2 <= r) {
        pointers = [
          {
            index: l,
            color: "#00ff00",
          },
          {
            index: m,
            color: "#00ffff",
          },
          {
            index: r,
            color: "#00ff00",
          },
          {
            index: i2,
            color: "#ff0000",
          },
        ];
        yield;
        buf.push(arr[i2++]);
      }

      for (let k = 0; k < buf.length; k++) {
        arr[l + k] = buf[k];
        pointers = [
          {
            index: l,
            color: "#00ff00",
          },
          {
            index: r,
            color: "#00ff00",
          },
          {
            index: l + k,
            color: "#ff0000",
          },
        ];
        yield;
      }
    }

    function* mergeSortInternal(l, r) {
      if (l < r) {
        const m = Math.floor((l + r) / 2);
        yield* mergeSortInternal(l, m);
        yield* mergeSortInternal(m + 1, r);
        yield* merge(l, m, r);
      }
    }

    yield* mergeSortInternal(0, arr.length - 1);
  }

  function* bubbleSort() {
    for (let i = 0; i < arr.length - 1; i++)
      for (let j = 0; j < arr.length - 1 - i; j++) {
        if (arr[j] > arr[j + 1]) {
          [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        }
        pointers = [
          {
            index: j,
            color: "#ff0000",
          },
          {
            index: j + 1,
            color: "#ff0000",
          },
        ];
        yield;
      }
  }

  function* selectionSort() {
    for (let i = 0; i < arr.length - 1; i++) {
      let min_index = i;
      for (let j = i + 1; j < arr.length; j++) {
        if (arr[j] < arr[min_index]) min_index = j;
        pointers = [
          {
            index: j,
            color: "#ff0000",
          },
          {
            index: i,
            color: "#00ff00",
          },
        ];
        yield;
      }
      [arr[min_index], arr[i]] = [arr[i], arr[min_index]];
    }
  }

  function* insertionSort() {
    let key;
    for (let i = 0; i < arr.length; i++) {
      key = arr[i];
      let j;
      for (j = i - 1; j >= 0 && arr[j] > key; j--) {
        arr[j + 1] = arr[j];
        pointers = [
          {
            index: j,
            color: "#ff0000",
          },
          {
            index: j + 1,
            color: "#ff0000",
          },
        ];
        yield;
      }
      arr[j + 1] = key;
    }
  }

  function* shellSort() {
    for (
      let gap = Math.floor(arr.length / 2);
      gap > 0;
      gap = Math.floor(gap / 2)
    ) {
      for (let i = gap; i < arr.length; i++) {
        let tmp = arr[i];
        let j;
        for (j = i; j >= gap && arr[j - gap] > tmp; j -= gap) {
          arr[j] = arr[j - gap];
          pointers = [
            {
              index: j,
              color: "#ff0000",
            },
            {
              index: j - gap,
              color: "#ff0000",
            },
          ];
          yield;
        }
        arr[j] = tmp;
      }
    }
  }

  function* quickSort() {
    function* quickSortInternal(l, r) {
      if (l >= r) return;

      let m = Math.floor((l + r) / 2);
      const pivot = arr[m];
      let i = l;
      let j = r;
      do {
        while (arr[i] < pivot) {
          pointers = [
            {
              index: l,
              color: "#00ff00",
            },
            {
              index: m,
              color: "#00ffff",
            },
            {
              index: r,
              color: "#00ff00",
            },
            {
              index: i,
              color: "#ff0000",
            },
            {
              index: j,
              color: "#ff0000",
            },
          ];
          yield;
          i++;
        }
        while (arr[j] > pivot) {
          pointers = [
            {
              index: l,
              color: "#00ff00",
            },
            {
              index: m,
              color: "#00ffff",
            },
            {
              index: r,
              color: "#00ff00",
            },
            {
              index: i,
              color: "#ff0000",
            },
            {
              index: j,
              color: "#ff0000",
            },
          ];
          yield;
          j--;
        }
        if (i <= j) {
          if (i === m) m = j;
          if (j === m) m = i;
          [arr[i], arr[j]] = [arr[j], arr[i]];
          i++;
          j--;
        }
      } while (i <= j);
      yield* quickSortInternal(l, j);
      yield* quickSortInternal(i, r);
    }

    yield* quickSortInternal(0, arr.length - 1);
  }

  function* heapSort() {
    function* downHeap(root, end) {
      while (true) {
        let comparingIndex = 2 * root + 1;
        if (comparingIndex > end) break;
        if (
          comparingIndex != end &&
          arr[comparingIndex + 1] > arr[comparingIndex]
        )
          comparingIndex++;
        if (arr[root] > arr[comparingIndex]) break;

        [arr[root], arr[comparingIndex]] = [arr[comparingIndex], arr[root]];
        pointers = [
          {
            index: root,
            color: "#ff0000",
          },
          {
            index: comparingIndex,
            color: "#ff0000",
          },
        ];
        yield;
        root = comparingIndex;
      }
    }

    for (let i = Math.floor(arr.length / 2) - 1; i >= 0; i--)
      yield* downHeap(i, arr.length - 1);

    for (let i = arr.length - 1; i > 0; ) {
      [arr[0], arr[i]] = [arr[i], arr[0]];
      pointers = [
        {
          index: 0,
          color: "#ff0000",
        },
        {
          index: i,
          color: "#ff0000",
        },
      ];
      yield;
      yield* downHeap(0, --i);
    }
  }

  let playing = false;
  let sortAlgorithms = [
    {
      text: "Bubble Sort",
      sort: bubbleSort,
    },
    {
      text: "Selection Sort",
      sort: selectionSort,
    },
    {
      text: "Insertion Sort",
      sort: insertionSort,
    },
    {
      text: "Shell Sort",
      sort: shellSort,
    },
    {
      text: "Quick Sort",
      sort: quickSort,
    },
    {
      text: "Heap Sort",
      sort: heapSort,
    },
    {
      text: "Merge Sort",
      sort: mergeSort,
    },
  ];
  let selectedAlgorithm = sortAlgorithms[0];
  let nextFlag = false;
  let frameId;
  let lastFrameTime;
  let arrayGenMethods = [
    {
      text: "Random",
      generate: () =>
        [...Array(arraySize)].map(() => Math.random() * maxHeight),
    },
    {
      text: "Ascending",
      generate: () =>
        [...Array(arraySize)].map((_, i) => ((i + 1) / arraySize) * maxHeight),
    },
    {
      text: "Descending",
      generate: () =>
        [...Array(arraySize)]
          .map((_, i) => ((i + 1) / arraySize) * maxHeight)
          .reverse(),
    },
  ];
  let selectedArrayGenMethod = arrayGenMethods[0];
  let arraySize = 32;
  let fps = 500;
  $: if (fps < 1) fps = 1;
  let startColorCode = "#ffff00";
  let endColorCode = "#ff0000";
  $: startColor = {
    r: Number("0x" + startColorCode.substring(1, 3)),
    g: Number("0x" + startColorCode.substring(3, 5)),
    b: Number("0x" + startColorCode.substring(5, 7)),
  };
  $: endColor = {
    r: Number("0x" + endColorCode.substring(1, 3)),
    g: Number("0x" + endColorCode.substring(3, 5)),
    b: Number("0x" + endColorCode.substring(5, 7)),
  };
  let totalSortInterval = 0;
  let totalRenderInterval = 0;
  let startColorPickerVisible = false;
  let endColorPickerVisible = false;
  let finished = false;

  let playStartTime; //for measurement

  const maxHeight = 700;

  const startSort = () => {
    const sortIter = selectedAlgorithm.sort();

    const sortNext = () => {
      const { done } = sortIter.next();
      if (done) {
        if (playStartTime) {
          console.log(Date.now() - playStartTime);
          playStartTime = undefined;
        }
        resetSettings();
        finished = true;
      }
    };

    const animate = (time) => {
      frameId = requestAnimationFrame(animate);

      if (!playing) {
        if (nextFlag) {
          nextFlag = false;
          sortNext();
        }
        lastFrameTime = time;
        return;
      }

      totalRenderInterval += time - lastFrameTime;
      if (totalRenderInterval >= totalSortInterval) {
        while (totalRenderInterval >= totalSortInterval) {
          sortNext();
          totalSortInterval += 1000 / fps;
        }
        totalSortInterval -= totalRenderInterval;
        totalRenderInterval = 0;
      }

      lastFrameTime = time;
    };

    frameId = requestAnimationFrame(animate);
    selectedAlgorithm.sort();
  };

  const resetSettings = () => {
    frameId && cancelAnimationFrame(frameId);
    if (playing) playing = false;
    if (finished) finished = false;
    pointers = [];
  };

  const initialize = () => {
    resetSettings();
    arr = selectedArrayGenMethod.generate();
    startSort();
  };

  const blend = (startColor, endColor, elem) => {
    const ratio = elem / maxHeight;
    const r = startColor.r * (1 - ratio) + endColor.r * ratio;
    const g = startColor.g * (1 - ratio) + endColor.g * ratio;
    const b = startColor.b * (1 - ratio) + endColor.b * ratio;
    return `rgb(${r},${g},${b})`;
  };

  onMount(initialize);
</script>

<div class="wrapper">
  <div class="histContainer" style="--height: {maxHeight}px">
    {#each arr as elem, index}
      <div
        class="bin"
        style="--bin-height: {elem}px; --color: {blend(
          startColor,
          endColor,
          elem
        )}"
      >
        {#each pointers.filter((pointer) => pointer.index === index) as pointer}
          <div
            class="pointer"
            style="--pointer-color: {pointer.color};
                 --offset: {pointer.color === '#ff0000' ? '-15px' : '-20px'}"
          />
        {/each}
      </div>
    {/each}
  </div>
  <div class="interface">
    <div class="frame">
      <div class="inputWrapper">
        <label for="arraySizeInput">Array Size</label>
        <select
          id="arraySizeInput"
          bind:value={arraySize}
          on:change={initialize}
        >
          {#each [...Array(7).keys()].map((i) => 8 * 2 ** i) as arraySizeOption}
            <option value={arraySizeOption}>{arraySizeOption}</option>
          {/each}
        </select>
      </div>
      <div class="inputWrapper">
        <label for="arrayGenMethodInput">Array Pattern</label>
        <select
          id="arrayGenMethodInput"
          bind:value={selectedArrayGenMethod}
          on:change={initialize}
        >
          {#each arrayGenMethods as arrayGenMethod}
            <option value={arrayGenMethod}>
              {arrayGenMethod.text}
            </option>
          {/each}
        </select>
      </div>
      <button on:click={initialize} class="resetButton">Initialize</button>
    </div>
    <div class="frame">
      <div class="inputWrapper">
        <label for="algorithmInput">Sort Type</label>
        <select
          id="algorithmInput"
          bind:value={selectedAlgorithm}
          on:change={initialize}
        >
          {#each sortAlgorithms as sortAlgorithm}
            <option value={sortAlgorithm}>
              {sortAlgorithm.text}
            </option>
          {/each}
        </select>
      </div>
      <button
        on:click={() => {
          playing = !playing;
          playStartTime = Date.now();
        }}
        disabled={finished}
      >
        {playing ? "Pause" : "Start"}
      </button>
      <button on:click={() => (nextFlag = true)} disabled={finished || playing}>
        Step Over
      </button>
    </div>
    <div class="frame fpsInputWrapper">
      <span>Speed</span>
      <div>
        <button on:click={() => (fps -= 100)}>---</button>
        <button on:click={() => (fps -= 10)}>--</button>
        <button on:click={() => (fps -= 1)}>-</button>
        <span>{fps}</span>
        <button on:click={() => (fps += 1)}>+</button>
        <button on:click={() => (fps += 10)}>++</button>
        <button on:click={() => (fps += 100)}>+++</button>
      </div>
    </div>
    <div class="frame">
      <div
        on:click={() => {
          startColorPickerVisible = true;
        }}
        class="inputWrapper"
      >
        <label for="startColor">Start Color</label>
        <div
          id="startColor"
          style="--color: {startColorCode}"
          class="colorSample"
        />
      </div>
      <div
        class="frame colorPickerWrapper"
        style={startColorPickerVisible ? "" : "display: none"}
      >
        <ChromaPicker bind:color={startColorCode} />
        <div
          on:click={() => (startColorPickerVisible = false)}
          class="closeButton"
        >
          <div />
          <div />
        </div>
      </div>
      <div
        class="inputWrapper"
        on:click={() => {
          endColorPickerVisible = true;
        }}
      >
        <label for="endColor">End Color</label>
        <div
          id="endColor"
          style="--color: {endColorCode}"
          class="colorSample"
        />
      </div>
      <div
        class="frame colorPickerWrapper"
        style={endColorPickerVisible ? "" : "display: none"}
      >
        <ChromaPicker bind:color={endColorCode} />
        <div
          on:click={() => (endColorPickerVisible = false)}
          class="closeButton"
        >
          <div />
          <div />
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  .wrapper {
    display: flex;
  }
  .histContainer {
    display: flex;
    align-items: flex-end;
    height: var(--height);
    flex: 1 1;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
    padding: 5px;
    padding-bottom: 25px;
  }
  .bin {
    position: relative;
    height: var(--bin-height);
    background-color: var(--color);
    flex: 1 1;
  }
  .pointer {
    position: absolute;
    bottom: var(--offset);
    left: 50%;
    transform: translateX(-50%) rotate(-45deg);
    border-style: solid;
    border-width: 2px 2px 0 0;
    padding: 3px;
    border-color: var(--pointer-color);
  }
  .interface {
    flex: 0 0 330px;
    padding: 0 8px;
  }
  .frame {
    padding: 15px;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.5);
  }
  .inputWrapper {
    display: flex;
    justify-content: space-between;
    margin-bottom: 5px;
  }
  button {
    width: 100%;
    height: 35px;
    background-color: #707070;
    color: #ffffff;
    border-radius: 4px;
    margin: 2px 0;
    padding: 0;
  }
  button:disabled {
    background-color: #444444;
  }
  select {
    width: 50%;
    background-color: #707070;
    color: #ffffff;
    border-radius: 4px;
    margin: 2px 0;
  }
  .fpsInputWrapper {
    display: flex;
    justify-content: space-between;
  }
  .fpsInputWrapper button {
    width: 32px;
    height: 32px;
  }
  .fpsInputWrapper div {
    display: flex;
  }
  .fpsInputWrapper span {
    vertical-align: middle;
    line-height: 32px;
  }
  .colorSample {
    display: inline-block;
    height: 30px;
    width: 30px;
    background-color: var(--color);
  }
  .colorPickerWrapper {
    position: relative;
    margin: 15px 0;
    padding: 30px;
  }
  .closeButton {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    position: absolute;
    top: 7px;
    right: 7px;
    background-color: white;
  }
  .closeButton div {
    position: absolute;
    width: 15px;
    height: 3px;
    top: 50%;
    left: 50%;
    background-color: black;
  }
  .closeButton div:nth-child(1) {
    transform: translate(-50%, -50%) rotate(45deg);
  }
  .closeButton div:nth-child(2) {
    transform: translate(-50%, -50%) rotate(-45deg);
  }
</style>
