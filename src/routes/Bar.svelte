<script>
  import { onMount, createEventDispatcher } from 'svelte';
  import { browser } from '$app/environment';
  import { scale } from 'svelte/transition';

  const dispatch = createEventDispatcher();

  function execCmd(cmd, value = null) {
    if (browser) {
      try {
        document.execCommand(cmd, false, value);
        dispatch('formatApplied', { cmd, value });
      } catch (err) {
        console.error(`Failed to execute command ${cmd}:`, err);
      }
    }
  }

  function changeFontSize() {
    const size = document.getElementById('size').value;
    execCmd('fontSize', size);
  }

  function changeFontColor() {
    const color = document.getElementById('fontColor').value;
    execCmd('foreColor', color);
  }

  function updateFontColor() {
    const color = document.getElementById('fontColor').value;
    document.getElementById('containFontColor').style.backgroundColor = color;
  }

  function changeBackColor() {
    const color = document.getElementById('backColor').value;
    execCmd('hiliteColor', color);
  }

  function updateBackColor() {
    const color = document.getElementById('backColor').value;
    document.getElementById('containBackColor').style.backgroundColor = color;
  }

  function handleAddClick() {
    dispatch('openAddModal');
  }

  onMount(() => {
    // No additional setup needed
  });
</script>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<div
class="container"
transition:scale={{ duration: 1000 }}
>
<div class="group group1">
  <select id="font" on:change={() => execCmd('fontName', document.getElementById('font').value)}>
    <option value="Arial" style="font-family: Arial;">Arial</option>
    <option value="'Times New Roman'" style="font-family: 'Times New Roman';">Times New Roman</option>
    <option value="Verdana" style="font-family: Verdana;">Verdana</option>
    <option value="Georgia" style="font-family: Georgia;">Georgia</option>
    <option value="Courier New" style="font-family: 'Courier New';">Courier New</option>
    <option value="cursive" style="font-family: cursive;">Cursive</option>
    <option value="fantasy" style="font-family: fantasy;">Fantasy</option>
    <option value="serif" style="font-family: serif;">Serif</option>
    <option value="monospace" style="font-family: monospace;">Monospace</option>
    <option value="Palatino" style="font-family: Palatino;">Palatino</option>
    <option value="Comic Sans MS" style="font-family: 'Comic Sans MS';">Comic Sans MS</option>
    <option value="Impact" style="font-family: Impact;">Impact</option>
    <option value="Lucida Console" style="font-family: 'Lucida Console';">Lucida Console</option>
  </select>
  <select id="size" on:change={changeFontSize}>
    <option value="1" style="font-size: 8px;">8</option>
    <option value="2" style="font-size: 10px;">10</option>
    <option value="3" style="font-size: 12px;">12</option>
    <option value="4" style="font-size: 14px;">14</option>
    <option value="5" style="font-size: 18px;">18</option>
    <option value="6" style="font-size: 24px;">24</option>
    <option value="7" style="font-size: 36px;">36</option>
    <option value="7" style="font-size: 48px;">48</option>
    <option value="7" style="font-size: 60px;">60</option>
    <option value="7" style="font-size: 72px;">72</option>
  </select>
</div>

<div class="group group2">
  <button on:click={() => execCmd('bold')} title="In đậm">
    <span class="material-symbols-outlined">format_bold</span>
  </button>
  <button on:click={() => execCmd('italic')} title="In nghiêng">
    <span class="material-symbols-outlined">format_italic</span>
  </button>
  <button on:click={() => execCmd('underline')} title="Gạch chân">
    <span class="material-symbols-outlined">format_underlined</span>
  </button>
  <button on:click={changeFontColor} title="Màu chữ">
    <span class="material-symbols-outlined">format_color_text</span>
  </button>
  <div id="containFontColor">
    <input type="color" id="fontColor" on:change={updateFontColor} />
  </div>
  <button on:click={changeBackColor} title="Màu nền">
    <span class="material-symbols-outlined">format_color_fill</span>
  </button>
  <div id="containBackColor">
    <input type="color" id="backColor" on:change={updateBackColor} />
  </div>
</div>

<div class="group group3">
  <button on:click={() => execCmd('justifyLeft')} title="Canh trái">
    <span class="material-symbols-outlined">format_align_left</span>
  </button>
  <button on:click={() => execCmd('justifyCenter')} title="Canh giữa">
    <span class="material-symbols-outlined">format_align_center</span>
  </button>
  <button on:click={() => execCmd('justifyRight')} title="Canh phải">
    <span class="material-symbols-outlined">format_align_right</span>
  </button>
  <button on:click={() => execCmd('justifyFull')} title="Canh đều">
    <span class="material-symbols-outlined">format_align_justify</span>
  </button>
</div>

<div class="group group4">
  <button on:click={handleAddClick} title="Thêm mới">
    <span class="material-symbols-outlined">add</span>
  </button>
</div>
</div>

<style>
:root {
  --bg-color: #ffffff;
  --text-color: #202124;
  --border-color: #dadce0;
  --hover-bg: #f1f3f4;
  --active-bg: #e8f0fe;
  --shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

.container {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 0.5rem;
  background: var(--bg-color);
  padding: 0 0.5rem;
  height: 4vh;
  position: relative;
  top: 0;
  z-index: 100;
  border-bottom: 1px solid var(--border-color);
  box-shadow: var(--shadow);
}

.group {
  display: flex;
  align-items: center;
  gap: 0.25rem;
  flex: 0 1 auto;
  min-width: 0;
}

.group1 select,
.group2 button,
.group2 input[type="color"],
.group3 button,
.group4 button {
  cursor: pointer;
  background: transparent;
  border: none;
  padding: 0 0.4rem;
  color: var(--text-color);
  border-radius: 4px;
  height: 2.5vh;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.2s, color 0.2s;
}

.group1 select:hover,
.group2 button:hover,
.group3 button:hover,
.group4 button:hover {
  background: var(--hover-bg);
}

.group1 select:active,
.group2 button:active,
.group3 button:active,
.group4 button:active {
  background: var(--active-bg);
}

#font,
#size {
  font-size: 0.8rem;
  padding: 0 0.4rem;
  color: var(--text-color);
  font-family: 'Roboto', sans-serif;
  background: var(--bg-color);

  border-radius: 4px;
  min-width: 70px;
  height: 2.5vh;
  line-height: 2.5vh;
}

#containFontColor,
#containBackColor {
  width: 1.8vh;
  height: 1.8vh;
  background-color: #ffffff;
  border-radius: 3px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid var(--border-color);
}

#fontColor,
#backColor {
  width: 100%;
  height: 100%;
  opacity: 0;
  padding: 0;
  margin: 0;
  border: none;
  cursor: pointer;
}

span.material-symbols-outlined {
  font-size: 2vh;
  color: var(--text-color);
  line-height: 2.5vh;
}

@media (max-width: 768px) {
  .container {
    flex-direction: row;
    flex-wrap: wrap;
    padding: 0.25rem;
    height: auto;
    min-height: 4vh;
  }

  .group {
    flex: 1 1 auto;
    justify-content: flex-start;
  }

  #font,
  #size {
    min-width: 60px;
    font-size: 0.75rem;
    height: 2.5vh;
  }

  .group1 select,
  .group2 button,
  .group3 button,
  .group4 button {
    height: 2.5vh;
  }

  span.material-symbols-outlined {
    font-size: 2vh;
  }

  #containFontColor,
  #containBackColor {
    width: 1.6vh;
    height: 1.6vh;
  }
}

@media (max-width: 480px) {
  .group1 select,
  .group2 button,
  .group3 button,
  .group4 button {
    padding: 0 0.3rem;
    height: 2.2vh;
  }

  span.material-symbols-outlined {
    font-size: 2vh;
  }

  #font,
  #size {
    font-size: 0.7rem;
    min-width: 50px;
    height: 2.2vh;
    line-height: 2.2vh;
  }

  #containFontColor,
  #containBackColor {
    width: 1.4vh;
    height: 1.4vh;
  }
}
</style>