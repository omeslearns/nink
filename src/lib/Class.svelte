<script>
  import { getLabel } from "../RDFapi";

  export let ciri;
  export let classNode;
</script>

<li
  on:click={() => (ciri = classNode)}
  class={`rounded-xl px-2 bg-red-300 cursor-pointer inline-block shadow capitalize`}
>
  {getLabel(classNode)}
</li>
