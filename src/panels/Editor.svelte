<script>
  import MdChild from "../components/MdChild.svelte";
  import Title from "../components/Title.svelte";
  import { namedNode, quad } from "../rdfun/Datafactory";
  import { rdf, x } from "../main";
  import { createClass, getLabel } from "../RDFapi";

  export let ciri;
  export let dataset;
  export let nestLevel = 0;
  let maxNest = 0;
  $: {
    maxNest = (() => {
      if ([...dataset]?.length > 500) return 1;
      if ([...dataset]?.length < 400) return 2;
      if ([...dataset]?.length < 300) return 3;
      if ([...dataset]?.length < 200) return 4;
      if ([...dataset]?.length < 100) return 5;
    })();
  }

  let relations = [];
  let markdown = namedNode("empty");

  $: classes = [...dataset.match(ciri, rdf.type)];
  $: isClass = [...dataset.match(null, rdf.type, ciri)]?.length;
  $: classInstances = [
    ...new Set([...dataset.match(null, rdf.type, ciri)].map((quad) => quad.subject.value))
  ].map(namedNode);

  $: propertys = [...dataset.match(ciri, null, null)].filter(
    (quad) => quad.object.termType == "Literal"
  );
  $: relations = [...dataset.match(ciri, null, null)].filter(
    (quad) => quad.object.termType == "NamedNode"
  );

  $: markdown = [...dataset.match(ciri, x.md, null)]?.[0]?.object ?? namedNode("test");

  $: subsAndObjs = [...dataset?.match(null, ciri, null)];
  $: isProperty = subsAndObjs?.length;
  $: propSubs = isProperty
    ? [...new Set(subsAndObjs.map((quad) => quad.subject.value))].map(namedNode)
    : null;
  $: propObjs = isProperty
    ? [...new Set(subsAndObjs.map((quad) => quad.object.value))].map(namedNode)
    : null;
</script>

<main class="bg-slate-200 col-span-60 row-span-full order-3 rounded-md overflow-auto">
  <div class="h-full px-8 {nestLevel == 0 ? 'py-5' : 'py-2'} flex flex-col gap-2">
    {#if nestLevel == 0}
      <h2 class="text-6xl">
        <Title bind:ciri />
      </h2>
    {/if}
    <ul class="px-0 flex justify-center flex-wrap gap-2">
      {#each classes as w, i}
        <li
          on:click={() => (ciri = w.object)}
          class={`rounded-xl px-2 bg-red-300 cursor-pointer inline-block shadow capitalize`}
        >
          {getLabel(w.object)}
        </li>
      {/each}
      <li
        class={`rounded-xl px-2 bg-white cursor-pointer inline-block shadow`}
        on:click={() => {
          // TODO: check if class exists
          const label = prompt("label of the new Class?");
          const classIRI = createClass(label);
          dataset.addQuads([quad(ciri, rdf.type, classIRI, x.Data)]);
          ciri = ciri;
        }}
      >
        + add Class
      </li>
    </ul>
    <details open>
      <summary>Attributes</summary>
      <ul class="list-inside list-disc mx-5">
        {#each propertys as propQuad}
          {#if !propQuad.predicate.value.includes("label")}
            <li class="">
              <span>
                <span
                  on:click={() => (ciri = propQuad.predicate)}
                  class={`px-1 mr-2 bg-sky-200 cursor-pointer before:content-["-"] after:content-[">"]`}
                >
                  {getLabel(propQuad.predicate)}
                </span>
                <span class="text-orange-400">{`"${propQuad.object.value}"`}</span>
              </span>
              <select name="" id="">
                <option value="Text" default>Text</option>
                <option value="Number">Number</option>
                <option value="Date">Date</option>
              </select>
            </li>
          {/if}
        {/each}
      </ul>
    </details>
    <details open>
      <summary>Relations</summary>
      <ul class="mx-0.5 list-inside list-disc">
        {#each relations as w, i}
          {#if !w.predicate.value.includes("http://www.w3.org/1999/02/22-rdf-syntax-ns#type") && !w.predicate.value.includes("/md") && !w.predicate.value.includes("/mdChild")}
            <li class="my-0.5">
              <div class="inline-block justify-between">
                <span class="flex align-top">
                  <span
                    on:click={() => (ciri = w.predicate)}
                    class={`px-1 mr-2 bg-sky-200 cursor-pointer before:content-["-"] after:content-["->"]`}
                  >
                    {getLabel(w.predicate)}
                  </span>
                  <span>
                    <details class="inline">
                      <summary>
                        <span
                          class="cursor-pointer bg-green-300/75"
                          on:click={() => (ciri = w.object)}>{getLabel(w.object)}</span
                        >
                      </summary>
                      {#if nestLevel <= maxNest}
                        <svelte:self
                          {...{
                            ciri: w.object,
                            dataset,
                            nestLevel: ++nestLevel
                          }}
                        />
                      {/if}
                    </details>
                  </span>
                </span>
              </div>
            </li>
          {/if}
        {/each}
      </ul>
    </details>
    {#if isClass}
      <details open>
        <summary>Instances of this Class</summary>
        <ul class="mx-0.5  gap-1 ">
          {#each classInstances as instance}
            <li>
              <details class="inline">
                <summary>
                  <span class="cursor-pointer bg-green-300/75" on:click={() => (ciri = instance)}
                    >{getLabel(instance)}</span
                  >
                </summary>
                {#if nestLevel < maxNest}
                  <svelte:self
                    {...{
                      ciri: instance,
                      dataset,
                      nestLevel: ++nestLevel
                    }}
                  />
                {/if}
              </details>
            </li>
          {/each}
        </ul>
      </details>
    {/if}
    {#if isProperty}
      <details open>
        <summary>"Triples"</summary>
        <ul class="mx-0.5  gap-1 list-inside list-disc ">
          {#each subsAndObjs as quad}
            <li>
              <span
                on:click={() => (ciri = quad.subject)}
                class={`px-1 mr-2 cursor-pointer bg-green-300/75`}
              >
                {getLabel(quad.subject)}
              </span>
              <span
                on:click={() => (ciri = quad.predicate)}
                class={`px-1 mr-2 bg-sky-200 cursor-pointer before:content-["-"] after:content-["->"]`}
              >
                {getLabel(quad.predicate)}
              </span>
              <span
                on:click={() => (ciri = quad.object)}
                class={`px-1 mr-2 cursor-pointer bg-green-300/75`}
              >
                {getLabel(quad.object)}
              </span>
            </li>
          {/each}
        </ul>
      </details>
      <details open>
        <summary>"Subjects"</summary>
        <ul class="mx-0.5  gap-1 list-inside list-disc ">
          {#each propSubs as w}
            <li>
              <span on:click={() => (ciri = w)} class={`px-1 mr-2 cursor-pointer bg-green-300/75`}>
                {getLabel(w)}
              </span>
            </li>
          {/each}
        </ul>
      </details>
      <details open>
        <summary>"Objects"</summary>
        <ul class="mx-0.5 gap-1 list-inside list-disc ">
          {#each propObjs as w}
            <li>
              <span on:click={() => (ciri = w)} class={`px-1 mr-2 cursor-pointer bg-green-300/75`}>
                {getLabel(w)}
              </span>
            </li>
          {/each}
        </ul>
      </details>
    {/if}
    {#if classes.some((quad) => quad.object.equals(x.Block))}
      <details open>
        <summary>Notes</summary>
        <!-- {#each children as childQuad} -->

        <MdChild bind:ciri {...{ dataset, markdown: ciri }} />
        <!-- {/each} -->
      </details>
    {:else}
      <details open>
        <summary>Notes</summary>
        <MdChild bind:ciri {...{ dataset, markdown }} />
      </details>
    {/if}
  </div>
</main>
