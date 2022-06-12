<script>
  // import N3 from "n3";
  import { createNode } from "../RDFapi";

  export let dataset;
  export let ciri;
  export let tabs;

  function download(filename, text) {
    var element = document.createElement("a");
    element.setAttribute("href", "data:text/turtle;charset=utf-8," + encodeURIComponent(text));
    element.setAttribute("download", filename);

    element.style.display = "none";
    document.body.appendChild(element);

    element.click();

    document.body.removeChild(element);
  }
  function downloadTTL() {
    // const writer = new N3.Writer();
    // writer.addQuads([...dataset.match()]);
    // writer.end((error, result) => {
    //   download("graph.ttl", result);
    // });
  }
</script>

<header
  class="bg-blue-300 px-5 grid grid-cols-3 place-items-center col-span-full row-span-5 order-1 rounded-md"
>
  <div class="flex gap-2 justify-self-start ">
    👀 View Filters
    <button on:click={downloadTTL}>💾 Save</button>
    <button on:click={downloadTTL}>⬇️ Download .ttl</button>
  </div>
  <button class="px-2 h-8 rounded bg-white">
    {ciri.value}
  </button>

  <div class="justify-self-end">
    <button
      type="button"
      class="hidden sm:flex items-center w-72 text-left space-x-3 px-4 h-8 bg-white ring-1 ring-slate-900/10 hover:ring-slate-300 focus:outline-none focus:ring-2 focus:ring-sky-500 shadow-sm rounded-lg text-slate-400 dark:bg-slate-800 dark:ring-0 dark:text-slate-300 dark:highlight-white/5 dark:hover:bg-slate-700"
    >
      <svg
        width="24"
        height="24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
        class="flex-none text-slate-300 dark:text-slate-400"
        aria-hidden="true"
      >
        <path d="m19 19-3.5-3.5" />
        <circle cx="11" cy="11" r="6" />
      </svg>
      <span class="flex-auto">Quick search...</span>
      <kbd class="font-sans font-semibold dark:text-slate-500">
        <abbr title="Command" class="no-underline text-slate-300 dark:text-slate-500">⌘</abbr> K
      </kbd>
    </button>
  </div>
</header>
