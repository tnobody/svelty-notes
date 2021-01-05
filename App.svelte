<script lang="ts">
  let canvas;
  const createNote = ({ content = "", pos = [0, 0] } = {}) => ({
    content,
    pos
  });
  let notes = [];
  const addNote = e => {
    if (e.target === canvas) {
      const { clientX, clientY } = e;
      notes = [...notes, createNote({ pos: [clientX, clientY] })];
    }
  };

  const removeNote = index => e => {
    notes = notes.filter((_, i) => i !== index);
  };

  let lastPos = null;
  let isDragging = null;
  const startDrag = index => ({ clientX, clientY }) => {
    lastPos = [clientX, clientY];
    isDragging = index;
  };
  const whileDragging = ({ clientX, clientY }) => {
    if (isDragging != null) {
      const {
        pos: [x, y],
        ...currentNote
      } = notes[isDragging];
      const [lastX, lastY] = lastPos;
      const [distanceX, distanceY] = [clientX - lastX, clientY - lastY];
      notes[isDragging] = { ...currentNote, pos: [x + distanceX, y + distanceY] };
      lastPos = [clientX, clientY];
    }
  };
  const stopDrag = dragEvent => {
    isDragging = null;
    lastPos = null;
  };
</script>
<style>
</style>
<svelte:head>
  <link href="https://unpkg.com/tailwindcss@^2/dist/tailwind.min.css" rel="stylesheet">
</svelte:head>
<main 
  on:click={addNote}
  on:pointermove={whileDragging} 
  on:pointerup={stopDrag} 
  class="absolute top-0 left-0 w-screen h-screen flex"
  bind:this={canvas}
>
  {#if notes.length === 0}
    <div class="container pointer-events-none mx-auto my-auto text-4xl md:text-6xl p-4 text-center text-gray-400 font-thin animate-pulse">      
      <span class="hidden md:inline">Click</span>
      <span class="inline md:hidden">Tap</span>
      somewhere to add a new note
    </div>
  {/if}
  {#each notes as {content, pos: [x,y]}, i}
    <div 
     style="transform: translate({x}px, {y}px) "
      class="absolute min-w-12"
      >
    <div 
      class="flex flex-col min-x-12 transition-all ease-out duration-100 select-none "
      style="min-width: 5rem;touch-action: none; transform: rotate({isDragging === i ? '-2': '0'}deg)"
      on:pointerdown={startDrag(i)}
      on:pointermove={whileDragging}
      on:pointerup={stopDrag}
    >
      <div class="flex">
        <div class="flex-1"></div>
        <button class="px-1" on:click={removeNote(i)}>&times;</button>
      </div>
      <div class="p-2 bg-yellow-200 {isDragging === i ? 'shadow-xl' : 'shadow-lg'}" autofocus contenteditable bind:innerHTML={content}  />
    </div>
    </div>
  {/each}
</main>