<script>
  import Chart from 'chart.js/auto';
  import { onMount } from 'svelte';
  export let data
  let actual_x = data["hourly"]["temperature_2m"]
  let not_y = data["hourly"]["time"]
  let actual_y = []
  const days =["Sun","Mon","Tue","Wed","Thu","Fri","Sat"] 
  for(let i=0;i<not_y.length;i++){
    let actual_days = new Date(not_y[i]*1000)
    actual_y.push(`${days[actual_days.getDay()]}:${actual_days.getHours()}`)
  }
  onMount(()=> {
    Chart.defaults.color = "#000"
    // @ts-ignore
    new Chart(document.getElementById("myChart"), {
      type: 'line',
      data: {
        labels: actual_y,
        
        datasets: [{
          label: '# Temperature in ℃',
          data: actual_x,
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
  })
  
</script>
<div style="width: 800px;">
  <canvas id="myChart"></canvas>
</div>