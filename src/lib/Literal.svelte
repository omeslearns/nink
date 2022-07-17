<script>
  export let ciri;
  export let propQuad;

  $: value = propQuad.object.value;
</script>

<input
  class="text-orange-400 "
  on:input={(e) => {
    propQuad.object.value = e.target.value;
    ciri = ciri;
  }}
  {value}
/>
