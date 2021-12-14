<script>
  import { createEventDispatcher } from "svelte";

  import { attempted, counter, disable1, disable2 } from "../data-store.js";
  import { unattempted } from "../data-store.js";

  import { currentitem } from "../data-store.js";

  import { question } from "../data-store.js";

  import { allques } from "../data-store.js";

  let allitems = true;
  let t;
  var count;
  var s = [];
  var un;
  var s = [];
  var q = [];
  var un = [];
  var final = [];
  var dummy = [];
  var showatt = false;
  var showunatt = false;
  var clicked = false;
  const dispatch = createEventDispatcher();

  function closeModal() {
    dispatch("cancel");

    counter.update((its) => {
      return $currentitem;
    });
    if ($counter == 10) {
      disable1.set(true);
    }
  }

  allques.subscribe((its) => {
    let t = its.filter((c, index) => {
      return its.indexOf(c) === index;
    });
    q = [...t];
  });

  attempted.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    s = [...s, ...t];
    count = t.length;
  });

  for (let i of s) {
    let p = $allques.indexOf(i);

    delete $allques[p];
  }
  var cou = 0;
  for (let b of $allques) {
    if (b != undefined) {
      un.push(b);
    }
  }

  unattempted.update((its) => {
    return [...un];
  });
  unattempted.subscribe((items) => {
    let e = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    final = [...final, ...e];
  });

  $: t = count;
  $: un = un.length;

  function showattempt() {
    showatt = true;
    showunatt = !showatt;
    allitems = false;
  }

  function showunattempt() {
    showunatt = !showunatt;
    showatt = false;
    allitems = false;
  }

  function showitems() {
    allitems = true;
    showatt = false;
    showunatt = false;
  }

  function goto(x, event) {
    clicked = true;
    question.subscribe((ies) => {
      var z = ies.indexOf(event);

      currentitem.set(z);
      if ($currentitem == 0) {
        disable2.set(true);
        disable1.set(false);
      }

      if ($currentitem > 0) {
        disable2.set(false);
        disable1.set(false);
      }
    });
  }
  question.subscribe((item) => {
    for (let y = 0; y < item.length; y++) {
      let x = JSON.parse(item[y].content_text).question;
      dummy.push(x);
    }
  });

  function gotoa(y, event) {
    let p = dummy.indexOf(event);

    currentitem.update((its) => {
      return p;
    });

    if ($currentitem == 0) {
      disable2.set(true);
      disable1.set(false);
    }

    if ($currentitem > 0) {
      disable2.set(false);
      disable1.set(false);
    }
  }
</script>

<div class="modal-backdrop" on:click={closeModal} />
<div class="modal">
  <div class="heads">
    <h2 style="padding-bottom:0.3rem;"on:click={showitems} class:change={allitems && !showatt && !showunatt}>
      All Items:{dummy.length}
    </h2>
    <h2
      on:click={showattempt}
      class:change={!allitems && showatt && !showunatt}
    >
      Attempted:{t}
    </h2>
    <h2
      on:click={showunattempt}
      class:change={!allitems && !showatt && showunatt}
    >
      UnAttempted:{un}
    </h2>
  </div>

  {#if allitems}
    <div>
      {#each $question as items, v (items)}
        <div
          class="allitems"
          
          on:click={goto(v, items)}
        >
          {v + 1}.{JSON.parse(items.content_text).question}
        </div>
      {/each}
    </div>
  {/if}

  {#if showatt}
    <div class="container1">
      <div class="allitems" style="height:2rem;text-align:center;margin-top:2rem;padding-top:1rem;padding-bottom:1rem;"class:hide={s.length > 0}>
        No Questions Attempted
      </div>
      {#each s as dataItem, i (dataItem)}
        {#if s.length > 0}
          <div
            class="allitems"
            
            on:click={gotoa(i, dataItem)}
          >
            {i + 1}.{dataItem}
          </div>
        {/if}
      {/each}
    </div>
  {/if}

  {#if showunatt}
    <div>
      {#each $unattempted as dos, j (dos)}
        <div
          class="allitems"
          
          on:click={gotoa(j, dos)}
        >
          {j + 1}.{dos}
        </div>
      {/each}
    </div>
  {/if}
</div>

<style>
  .modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background: transparent;
    z-index: 10;
  }

  .modal {
    position: fixed;
    top: 81px;
    margin-left: 0px;
    width: 30%;
    height: 31rem;

    background: white;
    border-radius: 5px;
    z-index: 100;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
    overflow-y: scroll;
  }

  .container1 {
    display: flex;
    flex-direction: column;
    flex-wrap: wrap;
  }
  .allitems {
    /*border: 1px solid black;*/
    width: 90%;
    margin-bottom: 2rem;
    margin-top:2rem;
    margin-left:1rem;
    margin-right:4rem;
    text-align:center;
    padding:5px;

    font-size: 15px;
    background: lightblue;
    box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.26);
  }

  h2 {
    text-align: center;
    font-size: 14px;
    margin-left: 0.4rem;
    margin-right: 0.3rem;;
    background:pink;
    height:2rem;
    padding:0.3rem;
  }

  .heads {
    margin-left:2rem;
    display: flex;
    flex-direction: row;
    
  }
  .hide {
    display: none;
  }
  .change {
    color: red;
    font-weight: 600;
  }
  .toggle {
    color: darkgreen;
    font-weight: 700;
  }
  h2:hover{
     background-color:pink;
     box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.26);

    transition: 0.3s;
  }
</style>
