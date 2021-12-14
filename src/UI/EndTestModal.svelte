<script>
  import { createEventDispatcher } from "svelte";
  import { attempted } from "../data-store.js";
  import { unattempted } from "../data-store.js";
  import Button from "./Button.svelte";
  import { question } from "../data-store.js";
  import { allques } from "../data-store.js";
  import { currentcorrect } from "../data-store.js";
  import {current} from "../data-store.js";
  import {disable1} from "../data-store.js"

  let d = [];
  let count = 0;
  var s = [];
  var q = [];
  var un = [];
  var curr = [];
  

  allques.subscribe((its) => {
    q = [...q, ...its];
  });

  question.subscribe((items) => {
    d = items;
  });

  attempted.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    s = [...s, ...t];
    count = t.length;
  });

  for (let i of s) {
    let p = q.indexOf(i);

    delete q[p];
  }
  var cou = 0;
  for (let b of q) {
    if (b != undefined) {
      un.push(b);
    }
  }

  unattempted.update((its) => {
    return [...its, ...un];
  });
  unattempted.subscribe((items) => {});

  currentcorrect.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    curr = [...curr, ...t];
  });

  $: v = count;
  $: l = un.length;

  const dispatch = createEventDispatcher();

  function closeModal() {
    dispatch("cancel");

  
  }
  function confirmModal() {
    dispatch("confirm");
    current.set(0);
    
  }

</script>

<div class="modal-backdrop" on:click={closeModal} />
<div class="modal">
  <h2>Attempted:{v}</h2>
  <h2>UnAttempted:{l}</h2>
  <h2>Do You Want To End Test?</h2>

  <div class="content">
    <slot />
  </div>

  <footer>
    <slot name="footer">
      <Button
        caption="Close"
        style="margin-left:13rem;"
        on:click={closeModal}
      />
      <Button
        caption="Confirm"
        style="margin-left:1rem;"
        on:click={confirmModal}
      />
    </slot>
  </footer>
</div>

<style>
  .modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100vh;
    background: rgba(0, 0, 0, 0.75);
    z-index: 10;
  }

  .modal {
    border:5px solid #00b3b3;
    position: fixed;
    top: 18vh;
    left: 10%;
    width: 80%;
    height: 15rem;
    max-height: 80vh;
    background: white;
    border-radius: 5px;
    z-index: 100;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  }

  .content {
    padding: 1rem;
  }

  footer {
    position: fixed;
    top: -80px;

    padding: 1rem;
    display: flex;
    justify-content: center;
  }
  h2 {
    text-align: center;
    font-size: 15px;
    font-weight:700;
  }

  @media (min-width: 768px) {
    .modal {
      width: 40rem;
      left: calc(50% - 20rem);
    }
  }
</style>
