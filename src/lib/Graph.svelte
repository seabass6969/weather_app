<script>
  import Chart from 'chart.js/auto';
  import { onMount } from 'svelte';
  export let data
  export let data_showing = "temperature_2m"
  export let data_title = "temperature in ℃"
  let actual_x = data["hourly"][data_showing]
  let not_y = data["hourly"]["time"]
  let actual_y = []
  const days =["Sun","Mon","Tue","Wed","Thu","Fri","Sat"] 
  for(let i=0;i<not_y.length;i++){
    let actual_days = new Date(not_y[i]*1000)
    // actual_y.push(`${days[actual_days.getDay()]}:${actual_days.getHours()}`)
    actual_y.push(`${days[actual_days.getDay()]}`)
  }
  let mychart
  let firsttimestart = true 
  function createChart(lables, data){
    // used to reload object before bug appear
    if(firsttimestart == false){
        mychart.destroy()
    }
    // @ts-ignore
    mychart = new Chart(document.getElementById(data_showing), {
      type: 'line',
      data: {
        labels: lables,
        
        datasets: [{
          label: data_title,
          data: data,
          borderColor: '#E67E22',
          backgroundColor: '#E74C3C',
          borderWidth: 1
        }]
      },
      options: {
        plugins: {
          legend: {
            labels: {
              font:{
                family: "'Numans', sans-serif"
              }
            }
          }
        },
        scales: {
          y: {
          }
        }
      }
    });
    firsttimestart = false
  }
  function changeContentChart(time){
    actual_x = []
    actual_y = []
    for(let i=0; i < time+1; i++){
        actual_x.push(data["hourly"][data_showing][i])
        let actual_days = new Date(not_y[i]*1000)
        actual_y.push(`${days[actual_days.getDay()]}:${actual_days.getHours()}`)
    }
    createChart(actual_y, actual_x)
  }
  onMount(()=> {
    Chart.defaults.color = "#000"
    createChart(actual_y, actual_x)
  })
  
</script>
<div class="row">
<button on:click={()=> {changeContentChart(12)}}>12 Hours</button>
<button on:click={()=> {changeContentChart(24)}}>24 Hours</button>
<button on:click={()=> {changeContentChart(48)}}>48 Hours</button>
<button on:click={()=> {changeContentChart(168)}}>Weeks</button>
</div>
<div style="width: 800px;">
  <canvas id="{data_showing}"></canvas>
</div>
<style>
    .row{
        display: grid;
        grid-template-columns: auto auto auto auto;
        width: 800px
    }
</style>
