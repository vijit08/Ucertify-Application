<script>
  import Button from "./Button.svelte";
  import EndTestModal from "./EndTestModal.svelte";
  import { question } from "../data-store.js";
  import { correctans } from "../data-store.js";

  import { allques } from "../data-store.js";
  import { onMount } from "svelte";
  import { attempted } from "../data-store.js";
  import { currentcorrect } from "../data-store.js";
  import { selectedanswer } from "../data-store.js";
  import { createEventDispatcher } from "svelte";
  import { currentitem } from "../data-store.js";
  import { counter } from "../data-store.js";

  import { disable1 } from "../data-store.js";
  import { disable2 } from "../data-store.js";
  import { list } from "../data-store.js";
  import {correctques} from "../data-store.js"
  //export let style;
  let dispatch = createEventDispatcher();
  
  var count = 0;

  let isopen = false;
  let seconds = 60;
  let minutes = 60;
  let timer;
  let correct = [];
  let questions = [];
  let correctall = [];
  let a = [];
  var questioncorrect=[];

  var iscorrect = [];
  var vijit = [];

  var currentselect = [];

  //=========================================MAIN LOGIC FUNCTION================================================
  function toggleattempt(z, event) {
    vijit.push(event);

    for (let i = 0; i < correct.length; i++) {
      let x = JSON.parse(correct[i].content_text).question;
    }
    selectedanswer.update((its) => {
      return [...vijit];
    });

    selectedanswer.subscribe((items) => {
      let t = items.filter((c, index) => {
        return items.indexOf(c) === index;
      });
      currentselect = [...t];
    });

    //==========================================TO CHECK CURRENT ANSWER IS RIGHT OR NOT==========================
    $: if ($correctans.includes(event)) {
      iscorrect.push(event);
      questioncorrect.push(z);

      correctques.update(items=>{
        return [...questioncorrect];
      })

      currentcorrect.update((its) => {
        return [...iscorrect];
      });
      currentcorrect.subscribe((item) => {});
    }

    //===============================================================================================================
    a.push(z);

    //==================ATTEMPTED LOGIC==================================
    attempted.update((its) => {
      return [...a];
    });
  }

  attempted.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    count = t.length;
  });
  //=======================================================================

  //===========================CORRECT ANSWERS OF ALL QUESTIONS LOGIC=============================================
  question.subscribe((items) => {
    correct = items;
  });

  for (let i = 0; i < correct.length; i++) {
    let x = JSON.parse(correct[i].content_text).question;
    questions.push(x);

    if (JSON.parse(correct[i].content_text).answers[0].is_correct == 1) {
      correctall.push(JSON.parse(correct[i].content_text).answers[0].answer);
    }
    if (JSON.parse(correct[i].content_text).answers[1].is_correct == 1) {
      correctall.push(JSON.parse(correct[i].content_text).answers[1].answer);
    }

    if (JSON.parse(correct[i].content_text).answers[2].is_correct == 1) {
      correctall.push(JSON.parse(correct[i].content_text).answers[2].answer);
    }
    if (JSON.parse(correct[i].content_text).answers[3].is_correct == 1) {
      correctall.push(JSON.parse(correct[i].content_text).answers[3].answer);
    }
  }

  correctans.update((items) => {
    return [...correctall];
  });

  //=====================================================================================================================

  //========================================ALL QUESTIONS LOGIC========================================================
  allques.update((items) => {
    return [...questions];
  });
  //===================================================================================================================

  //=====================================TIMER LOGIC========================================================
  function start() {
    if (minutes === 0 && seconds == 0) {
      if (timer) {
        clearInterval(timer);
        timer = null;
      }
    }

    onMount(() => {
      timer = setInterval(() => {
        seconds--;
        if (seconds == 0) {
          minutes--;
          seconds = 60;
          if (minutes == 0) {
            minutes = 0;
          }
        }
      }, 1000);
    });
  }
  start();
  //=============================================================================================================

  //==========================================NEXT AND PREVIOUS BUTTON LOGIC====================================
 if($counter==0){
   disable1.set(false);
   disable2.set(true);
 }
 
 
  function next() {
    disable1.set(false);
    disable2.set(false);

    if ($counter == 9) {
      disable1.set(true);
    }
    dispatch("n");
  }

  function prev() {
    disable2.set(false);
    if ($counter == 1 || $counter <= 0) {
      disable1.set(false);
      disable2.set(true);
    }
    if ($counter <=10) {
      disable1.set(false);
      disable2.set(false);
    }
    dispatch("p");
  }
  //===============================================================================================================

  function end() {
    //END TEST EVENT
    dispatch("e");
  }

  function lis() {
    //OPEN LIST EVENT
    dispatch("l");
  }
</script>

<section class:shift={$list} >
  {#each $question as dataItem, i (dataItem)}
    {#if i == $currentitem}
      <p id="questions{i}" style="margin-left:5rem;margin-right:5rem;font-size:18px;">
        <b>{i + 1}. {JSON.parse(dataItem.content_text).question}</b>
      </p>
      <div id="question-section" style="margin-left:3rem;" >
        {#each JSON.parse(dataItem.content_text).answers as ans, index (ans)}
          <label for="ans{index}" id="option{index}" class="items">
            
           
            <input
            
              type="radio"
              name="ans"
              id="ans{index}"
              is_correct={ans.is_correct}
              value={ans.answer}
              on:click={toggleattempt(
                JSON.parse(dataItem.content_text).question,
                ans.answer
              )}
              checked={currentselect.includes(ans.answer) ? true : false}
            />{@html ans.answer}
          </label>
        {/each}
      </div>
      <div class="questions">
        <div class="timer">{minutes}:{seconds}</div>
        <Button
          style="margin-top:0;margin-left:2rem;"
          type="button"
          caption="List"
          on:click={lis}
        />
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
          caption="End Test"
          on:click
          on:click={end}
        />

        {#if isopen}
          <EndTestModal />
        {/if}
      </div>
    {/if}
  {/each}
</section>

<style>
  #questions {
    margin-left: 2.5rem;
  }
  #question-section {
    display: flex;
    flex-direction: column;
    height: 200px;
  }
  .items {
    margin-bottom: 10px;
    font-size:18px;
    padding: 10px;
  }

  .questions {
    position: fixed;
   
    bottom: 0px;

    left: 0px;
    height: 5rem;
    width: 99.9%;

    background-image: linear-gradient(to right, red , yellow);
    box-shadow: 10px 6px 12px rgba(0, 0, 0, 0.26);
    display: flex;
    margin-top: 5rem;

    height: 4rem;
    align-items: center;
    justify-content: center;
  }

  section {
    margin-top: 6rem;

    height: 16rem;
    position: fixed;
  }

  input {
    margin-left: 2rem;
    margin-right:2rem;
  }
  #option {
    margin-left: 2rem;
  }

  .numbering {
    margin-left: 2rem;
  }

  .timer {
    margin-left: 2rem;
    font-weight: 700;
  }
  .shift {
    margin-left: 26rem;
  }
  
</style>

