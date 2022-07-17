<script lang="ts">

  export let ciri;
  export let propQuad;
  export let att;
  console.warn(ciri)
  let isClass = false;
  let isProperty = false;

  $: title = propQuad.object.value;
</script>

<textarea
  on:input={(e) => {
    propQuad.object.value = e.target.value;
    ciri = ciri;
  }}
  rows={title.split("\n")?.length ?? 1}
  class:bg-red-300={isClass}
  class:bg-sky-200={isProperty}
  class:bg-green-300={!isClass && !isProperty}
  class:w-full={!att}
  class={"px-2 inline-bloc grow overflow-hidden rounded-xl" +
    (isProperty ? `before:content-["-"] after:content-["->"]` : "")}
  >{title}</textarea
>
