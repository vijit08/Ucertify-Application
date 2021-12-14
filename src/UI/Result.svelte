<script>

  import { question, unattempted } from "../data-store.js";
  import { attempted } from "../data-store.js";
  import { allques } from "../data-store.js";
  import { currentcorrect } from "../data-store.js";
  import { allincorrect } from "../data-store.js";
  import { current } from "../data-store.js";
  import { selectedanswer } from "../data-store.js";
  import { disable1,disable2 } from "../data-store.js";
  import {correctques} from "../data-store.js";
  import Nav from "./Nav.svelte";
  import {timetaken} from "../data-store.js";

  import Review from "./Review.svelte";
  let Heading;
  var m;
  var result = 0.0;
 
  var count = 0;
  var dummyarray = [];
  var correctlength;
  var review = false;
  var currentselect = [];
  var incorrect = [];
  var curr=[];
  var questioncorrect=[];
 

  attempted.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    dummyarray = [...t];
    count = t.length;
  });

  unattempted.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    m = t.length;
  });

  currentcorrect.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });

    curr=[...t];
    correctlength = t.length;
  });

  $: correctlength = correctlength;

  $: result = Math.round((correctlength / 11) * 100);

  function r(x) {
    review = true;
    $: current.update((its) => {
      return x;
    });
    if (x == 0) {
      $disable2 = true;
   
    }
   
    
  }

  selectedanswer.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });
    currentselect = [...t];
  });
  allincorrect.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });
    incorrect = [...t];
  });


 correctques.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });
    questioncorrect = [...t];
  });


 
</script>

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">


<header>
    <Nav Heading={"uCertify Test Result"}/>
  </header>

<div class="container">
  <div class="items"><b style="color:purple;">{result}%</b><br>Result</div>
    <div class="items" ><b style="color:#009999;">{$allques.length}</b><br>All Items</div>
    <div class="items" ><b style="color:green;"><i class="fa fa-check" style="color:green;"></i>{correctlength}</b><br>Correct</div>
    <div class="items" ><b style="color:red;"><i class="fa fa-close" style="color:red;"></i>{count - correctlength}</b><br>Incorrect</div>
    <div class="items"><b style="color:magenta;">{count}</b><br>Attempted</div>
    <div class="items"><b style="color:orange;"><i class="fa fa-eye-slash" style="color:orange;"></i>{m}</b><br>Unattempted</div>
  
  <br />
  <!--<div class="value">
    <div class="it">{$allques.length}</div>
    <div class="it">{count}</div>
    <div class="it">{m}</div>
    <div class="it">{correctlength}</div>
    <div class="it">{count - correctlength}</div>
  </div>-->
</div>

<div>
 <!--<div class="result">Total Result:{result}%Time:{$timetaken}</div>-->

  <table id="demo">
    <tr>
      <th id="first">Index No</th>
      <th id="second">Questions</th>
      <th id="third">Answers</th>
    </tr>

    {#each $question as dataItem, i (dataItem)}
      <tr >
        <td style="text-align:center;">{i + 1}</td>
        
        <td id="questions"
          on:click={r(i)} >
            
           {i + 1}.{JSON.parse(dataItem.content_text).question}
                
          </td
        >

        <td
          ><div style="text-align:center">
            {#each JSON.parse(dataItem.content_text).answers as ans, index (ans)}
              <span
                is_correct={ans.is_correct}
                class="dot"
                class:success={(currentselect.includes(ans.answer) &&
                  ans.is_correct == 1) ||
                  ans.is_correct == 1}
                class:unsuccess={currentselect.includes(ans.answer) &&
                  ans.is_correct == 0}
                
                id="ans{index}"
                value={ans.answer}
              >
                {index + 1}</span
                >

                
              
              
                
           

            
              
            {/each}
            {#if !dummyarray.includes(JSON.parse(dataItem.content_text).question)}
            <div class="tooltip"><i class="fa fa-eye-slash"></i>
              <span class="tooltiptext">Unattempted</span>
            </div>
            {/if}

            {#if dummyarray.includes(JSON.parse(dataItem.content_text).question)&& questioncorrect.includes(JSON.parse(dataItem.content_text).question)}
            <div class="tooltip"><i class="fa fa-check"></i>
              <span class="tooltiptext">Correct</span>
            </div>


            {:else if dummyarray.includes(JSON.parse(dataItem.content_text).question)&& !questioncorrect.includes(JSON.parse(dataItem.content_text).question)}
            <div class="tooltip"><i class="fa fa-close"></i>
              <span class="tooltiptext">Incorrect</span>
            </div>

            {/if}
            
            

        </div>
      </td>
      </tr>
    {/each}
  </table>
</div>
{#if review}
  <Review />
{/if}

<style>
  .container {
    display: flex;
    margin-top: 8rem;
    margin-left: 4rem;
    font-size: 20px;
    flex-direction: row;
    justify-content: start;
    padding: 10px;
    /*border:1px solid black;*/
    width: 90%;
  }

  .headings {
    display: flex;
    margin-top: 4rem;
    margin-left: 4rem;
    font-size: 20px;
    justify-content: start;
    padding: 5px;
    border: 1px solid red;
    width: 90%;

    height: 7.5rem;
  }
  .value {
    display: flex;
    margin-left: 0;
    margin-top: -70px;
    font-size: 20px;
    justify-content: start;
    padding: 1px;
    /*border:1px solid red;*/
    border:1px solid black;
    width: 90%;
  }

  .dot {
    height: 25px;
    width: 25px;
    background-color: white;
    border-radius: 50%;
    display: inline-block;
    border: 1px solid black;
  }

  .items {
    margin-left: 1rem;
    display:inline-block;
   /* border: 1px solid black;*/
    height:4rem;
    width:50%;
    padding-top:1rem;
    font-size:17px;
    text-align:center;
    background-color: #e6ffff;

    border:1px solid #009999;

  }
  .resultbar{
      
width: 35px;
height: 5px;
background-color: black;
margin: 6px 0;
}
  .it {
    margin-left: 12.3rem;
  }
  table {
    margin: 6rem;

    border-top: 2px solid #00b3b3;
  }
  #first {
    padding-left: 4rem;
    padding-right: 4rem;
   /* border: 1px solid black;*/
  }
  #second {
    padding-left: 25rem;
    padding-right: 25rem;
    /*border: 1px solid black;*/
  }
  #third {
    padding-left: 4rem;
    padding-right: 4rem;
   /* border: 1px solid black;*/
  }
  td {
    /*border: 1px solid black;*/
  }
  :global(body) {
    overflow: scroll;
  }
  .success {
    border: 2px solid green;
  }
  .unsuccess {
    border: 2px solid red;
  }
  .result {
    display: inline-block;
    width: 30%;
    height: 4rem;
    margin-left: 30rem;
    margin-top: 3rem;
    color: red;
    font-weight: 600;
    font-size: 25px;
    text-align: center;
  }
  #demo {
    margin-top: 2rem;
    margin-left: 3rem;
  }

  .tooltip {
position: relative;
display: inline-block;
margin-left:1rem;

}

.tooltip .tooltiptext {
visibility: hidden;
width: 120px;
background-color: black;
color: #fff;
text-align: center;
border-radius: 6px;
padding: 5px 0;

/* Position the tooltip */
position: absolute;
z-index: 1;
top: -5px;
left: 105%;
}

.tooltip:hover .tooltiptext {
visibility: visible;
}
i{
    font-size:20px;
}

tr:nth-child(even) {background-color: #f2f2f2;}
tr:nth-child(even):hover{
  background-color:#e6ffff;
  transition:0.3s;
}
tr:nth-child(odd):hover{
  background-color:#e6ffff;
  transition:0.3s;
}

.items:hover{
  box-shadow: 0px 6px 12px rgba(0,0,0,0.26);
  background-color:#f5f5f0;
  border:none;
  transition:0.3s;
  
}

</style>
