<script>
  import { rdf, rdfs, x } from "../main";
  import { createClass, getLabel } from "../RDFapi";
  import { quad } from "../rdfun/Datafactory";

  let eventSource;

  export let ciri;
  let value = "";
  let input;
  let classes = [...dataset.match(null, rdf.type, rdfs.Class)].map((quad) => quad.subject);

  function addClass() {
    // TODO: check if class exists
    if (!value.replaceAll(" ", "") == false) {
      const classIRI = createClass(value);
      dataset.addQuads([quad(ciri, rdf.type, classIRI, x.Data)]);
      ciri = ciri;
    }
    value = "";
  }
</script>

<li class={`rounded-xl px-2 bg-white cursor-pointer inline-block shadow`}>
  <input
    class=""
    size="8"
    bind:this={input}
    type="text"
    list="list"
    bind:value
    on:change={addClass}
    on:keydown={(e) => (eventSource = e.key ? "input" : "list")}
    on:input={() => (eventSource == "list" ? addClass() : null)}
    placeholder="+Class"
  />
  <datalist id="list">
    {#each classes as classNode}
      <option value={getLabel(classNode)} />
    {/each}
    <option {value} on:click={() => console.log("Selected Class")}>new Class</option>
  </datalist>
</li>
