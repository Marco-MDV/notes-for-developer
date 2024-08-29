# <a href='https://www.chartjs.org/docs/latest/axes/styling.html'>Chart.JS</a> 📊

When we want to talk about chart in a web page maybe we'll use ChartJS this's library for creating charts, are present different type an example you can found in <a href='https://financemdv.vercel.app/'>Finance</a> different example and you can interact with their.
short list of chart types:
- Area Chart
- Bar Chart
- Bubble Chart
- Doughnut and Pie Charts 
- Line Chart
- Mixed Chart Types
- Polar Area Chart
- Radar Chart

If you never look chart of this library, I try to explain create a chart.
1. for a chart you need *data*:
    - value as money, workday or other but is essentially are number 
    - date as mouths , days, or syntax as: dd/mm/yyyy - mm/yyyy -mm/dd/yyyy
2. report all elemets that you want using as "Title","Legend" and other.
    ```
        import React from 'react';
        <-- report the chart you want -->
        import { Line } from 'react-chartjs-2';
        import {Chart as ChartJS,CategoryScale,LinearScale,PointElement,LineElement,Title,Tooltip,Legend} from 'chart.js';
        <-- report all component using in chart -->
        ChartJS.register(CategoryScale,LinearScale,PointElement,LineElement,Title,Tooltip,Legend);
    ```
3. we invoke the component:
    ```
        const data = {
                labels: [mon, tue, wed, thu, fri, sat, sun],
                datasets: [{
                    label: statusAction,
                    data: [1, 2, 3, 4, 5, 6, 7],
                    borderColor: '#000000',
                    backgroundColor: 'transparent',
                    pointBorderColor: '#000000'
                }]
            }

        const options = {
            responsive: true,
            plugins: {
                legend: {
                    display: false, //disable legend
                },
            },
            scales: {
                x: {
                    ticks: {
                        display: false,
                    },
                    grid: {
                        drawBorder: true,
                        display: true,
                        color: '#000000',
                    },
                },
                y: {
                    ticks: {
                        display: true,
                    },
                    grid: {
                        drawBorder: true,
                        display: true,
                        color: '#000000',
                    },
                }
        },
    };



        <Line
            data={data}
            options={options}
        />
    ```
Now that you know how to set the options, you can use the information for building a chart!


### I think that it's easier for you but if you have a question please write me on <a href='https://www.linkedin.com/in/marco-de-vincentiis-98299a217'>Linkedin</a>