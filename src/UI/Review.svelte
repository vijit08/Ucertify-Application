<script>
  import { question } from "../data-store.js";
  import { current } from "../data-store.js";
  import { currentcorrect } from "../data-store.js";
  import { currentitem } from "../data-store.js";
  import { createEventDispatcher } from "svelte";
  import { disable1, disable2 } from "../data-store.js";
  import Button from "./Button.svelte";
  import Nav from "./Nav.svelte";
  import {selectedanswer} from "../data-store.js";
  import App from "../App.svelte";
  import {rev} from "../data-store.js";
  import {startpage} from "../data-store.js";
  import {isconfirm} from "../data-store.js";

  let dispatch = createEventDispatcher();
  var explain = true;
  var currentselect=[];
  var home=false;
  let Heading;
  selectedanswer.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });
    currentselect = [...t];
  });
  

  if ($current >0) {
    disable2.set(false);
    
  }
  if($current==0){
    disable1.set(false);
  }

  if($current>=10){
    disable1.set(true);
  }
  function next() {
    $current += 1;

    disable1.set(false);
    disable2.set(false);

    if ($current == 10) {
      disable1.set(true);
    }
  }

  function prev() {
    $current -= 1;
    disable2.set(false);
    disable1.set(false);
    //disable1.set(false);
    if ($current == 0) {
      disable1.set(false);
      disable2.set(true);
    }
  }

  function dash(){
    home=true;
    rev.set(false);
    startpage.set(true);
    isconfirm.set(false);
    
    
    
  }
</script>
<header>
  <Nav Heading={"uCertify Test Review"}/>
</header>

<div class="review">
  <section>
    {#each $question as dataItem, i (dataItem)}
      {#if i == $current}
        <div id="questions{i}" style="margin-left:5rem;margin-right:5rem;font-size:18px;">
          <b>{i + 1}.  {JSON.parse(dataItem.content_text).question}</b>
        </div>
        <div id="question-section" style="margin-left:3rem;">
          {#each JSON.parse(dataItem.content_text).answers as ans, index (ans)}
            <label for="ans{index}" id="option{index}" style="font-size:18px;" class="radio" class:bold={ans.is_correct==1} class:notbold={currentselect.includes(ans.answer)&&ans.is_correct==0}>
              <input
                class="radio_input"
                type="radio"
                name="ans"
                id="ans{index}"
                is_correct={ans.is_correct}
                value={ans.answer}
                checked={ans.answer&&ans.is_correct==1?true : false}
                disabled
              />
              
              <div
                class:radio_radio={ans.is_correct == 1||ans.answer}
                
                class:wrong={currentselect.includes(ans.answer)&&ans.is_correct == 0}
              />
              {@html ans.answer}
            </label>
          {/each}

          {#if explain}
            <div class="explanation">
              {i + 1}.{@html JSON.parse(dataItem.content_text).explanation}
            </div>
          {/if}
        </div>
        <div class="questions">
          <Button
            style="margin-top:0;margin-left:2rem;"
            type="button"
            caption="Previous"
            disabled={$disable2}
            on:click={prev}
          />
          <div class="numbering">
            <b>{i + 1} of 11</b>
          </div>
          <Button
            style="margin-top:0;margin-left:2rem;"
            type="button"
            caption="Next"
            disabled={$disable1}
            on:click={next}
          />
          <Button
          style="margin-top:0;margin-left:2rem;"
          type="button"
          caption="DashBoard"
         
          on:click={dash}/>
        </div>
      {/if}
    {/each}
  </section>

  {#if home}
<App/>
  {/if}


 
</div>

<style>
  .review {
    position: fixed;
    top: 81px;
    margin-left: 0px;
    width: 100%;
    height: 100vh;

    background: white;
    border-radius: 5px;
    z-index: 100;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
    overflow: hidden;
  }
  #questions {
    border: 1px solid black;
    margin-left: 4rem;
  }
  #question-section {
    display: flex;
    flex-direction: column;
    height: 200px;
  }

  .questions {
    position: fixed;

    bottom: 0px;

    left: 0px;
    height: 5rem;
    width: 100%;
    background-image: linear-gradient(to right, red , yellow);
    box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.26);
    display: flex;
    margin-top: 5rem;
    /*border :1px solid black;*/
    height: 4rem;
    align-items: center;
    justify-content: center;
  }

  section {
    margin-top: 1rem;
    /*border:1px solid black;*/
    height: 16rem;
    position: fixed;
  }

  input {
    margin-left: 2rem;
  }

  #option {
    margin-left: 2rem;
  }

  .numbering {
    margin-left: 2rem;
  }

  .explanation {
    
   border-radius: 5px;
    background-color: lightblue;
    width: 90%;
    margin-left: 2.3rem;
    margin-top: 0.8rem;
    margin-right:2.5rem;
    margin-bottom:1rem;
    padding-top:2rem;
    padding-right:2rem;
    padding-left:2rem;
    padding-bottom:1rem;
    box-shadow: 0px 6px 12px rgba(0, 0, 0, 0.26);
   
  }

  .radio {
    display: inline-flex;
    align-items: center;
    cursor: center;
    margin-right: 10px;
    margin-left: 2rem;
    margin-top: 2rem;
    
  }

  .radio_input {
    margin-left: 4rem;
    display:none;
    

    
  }
  .radio_radio {
    width: 0.9em;
    height: 0.9em;
    background-color: #f5f5f0;
    border: 1px solid black;
    border-radius: 50%;
    margin-right: 10px;
    box-sizing: border-box;
    padding: 2px;
    
  }
  .radio_radio:after {
    content: "";
    width: 100%;
    height: 100%;
    display: block;
    background: green;
    border-radius: 50%;
    transform: scale(0);
  }

  .radio_input:checked + .radio_radio::after  {
    transform: scale(1);
  }

  .wrong{
    width: 0.9em;
    height: 0.9em;
    background-color: #f5f5f0;
    border: 1px solid black;
    border-radius: 50%;
    margin-right: 10px;
    box-sizing: border-box;
    padding: 2px;
  }
  .wrong:after {
    content: "";
    width: 100%;
    height: 100%;
    display: block;
    background: red;
    border-radius: 50%;
    transform: scale(1);
  }
  .bold{
    font-weight: 700;
    color:green;
  }
  .notbold{
    font-weight: 700;
    color:red;
  }
</style>
