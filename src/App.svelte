<script>
  import Nav from "./UI/Nav.svelte";
  import Button from "./UI/Button.svelte";

  import Footer from "./UI/Footer.svelte";

  import EndTestModal from "./UI/EndTestModal.svelte";
  import Result from "./UI/Result.svelte";

  import List from "./UI/List.svelte";
  import { current } from "./data-store.js";
  import { currentitem } from "./data-store.js";
  import { counter } from "./data-store.js";
  import { startpage } from "./data-store.js";
  import { isconfirm } from "./data-store.js";
  import { list } from "./data-store.js";

  let Heading;
  let contain;
  let style;
  var isopen = false;

  // var isconfirm = false;

  var isend = false;
  // var list = false;

  var firstpage = false;
  //var startpage = true;
  var reviewpage = true;

  function tooglestartpage() {
    startpage.set(false);
    firstpage = true;


   
    counter.set(0);
    current.set(0);
    currentitem.set(0);
  }
  function open() {
    isopen = true;
  }
  function cancelit() {
    isopen = !isopen;
  }
  function closelist() {
    list.set(false);
    firstpage = true;
  }
  function confirm() {
    isconfirm.set(true);
    isopen = false;
    startpage.set(true);
    firstpage = true;
    currentitem.set(0);
  }

  function listopen() {
    list.set(true);
  }

  function next() {
    $current += 1;
    $counter += 1;
    $currentitem += 1;
  }

  function prev() {
    $counter -= 1;
    $current -= 1;
    $currentitem -= 1;
  }

  function end() {
    isend = true;
  }

  function review() {
    //reviewpage = true;

    isconfirm.set(false);
  }

</script>

<header>
  <Nav Heading={"uCertify Test Prep"} />
</header>
<main>
  {#if $startpage}
    
    {#if $isconfirm}
      <Button
        style="margin-left:38rem;display:none;"
        type="button"
        caption="Start Test"
        on:click={tooglestartpage}
      />
      <Result on:res={review} />
    {:else}
      <Button
        style="margin-left:38rem;padding:1rem;"
        type="button"
        caption="Start Test"
        on:click={tooglestartpage}
      />
    {/if}
  {:else if !$startpage}
    {#if firstpage}
      <Footer
        on:l={listopen}
        on:click={open}
        on:n={next}
        on:p={prev}
        on:e={end}
      />{/if}
    <!-- {#if $list}
      
      <Footer
        style={"margin-left:26rem;"}
        on:l={listopen}
        on:click={open}
        on:n={next}
        on:p={prev}
        on:e={end}
      />-->
  {/if}

  {#if isopen}
    <EndTestModal on:cancel={cancelit} on:confirm={confirm} />
  {/if}

  {#if $list}
    <List on:cancel={closelist} />
  {/if}
</main>

<style>
</style>
