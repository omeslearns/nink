<script>
  import { getLabel } from "../RDFapi";

  export let ciri;
  export let property;
</script>

<span
  on:click={() => (ciri = property)}
  class={`px-1 mr-2 bg-sky-200 cursor-pointer before:content-["-"] after:content-[">"]`}
>
  {getLabel(property)}
</span>
