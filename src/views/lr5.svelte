<script>
    import Scatter from 'svelte-chartjs/src/Scatter.svelte';

    let x = [2, 4, 6, 8, 10],
        y = [4.5, 7.0, 8.0, 7.5, 9.0],
        x2 = x.map(item => item ** 2),
        y2 = y.map(item => item ** 2),
        xy = x.map((item, index) => item * y[index]),
        sum = {
            x: x.reduce((sum, item) => { return sum += item }, 0),
            y: y.reduce((sum, item) => { return sum += item }, 0),
            x2: x2.reduce((sum, item) => { return sum += item }, 0),
            y2: y2.reduce((sum, item) => { return sum += item }, 0),
            xy: xy.reduce((sum, item) => { return sum += item }, 0),
        },
        n = x.length,
        rxy = (n * sum.xy - (sum.x * sum.y)) / Math.sqrt(((n * sum.x2) - (sum.x ** 2)) * ((n * sum.y2) - (sum.y ** 2))),
        a0 = ((sum.y * sum.x2) - (sum.xy * sum.x)) / ((n * sum.x2) - (sum.x ** 2)),
        a1 = ((n * sum.xy) - (sum.x * sum.y)) / ((n * sum.x2) - (sum.x ** 2)),
        b0 = ((sum.x * sum.y2) - (sum.xy * sum.y)) / ((n * sum.y2) - (sum.y ** 2)),
        b1 = ((n * sum.xy) - (sum.x * sum.y)) / ((sum.y2 * n) - (sum.y ** 2)),
        checkAB = Math.sqrt(a1 * b1);
    console.log(checkAB);

    let data = {
        datasets: [{
            label: 'Scatter Dataset',
            data: x.map((item, index) => { return { x: item, y: y[index]} }),
            backgroundColor: '#FF6989',
            borderColor: '#f81946',
        }]
    };

    let options = {
        legend: {
            display: false,
        },
        scales: {
            xAxes: [{
                type: 'linear',
                position: 'bottom',
                stacked: true,
                ticks: {
                    min: 0,
                    max: 15
                }
            }],
            yAxes: [{
                type: 'linear',
                position: 'bottom',
                stacked: true,
                ticks: {
                    min: 0,
                    max: 15
                }
            }]
        }
    }
</script>

<div class="table-responsive mb-5">
    <table class="table table-bordered">
        <thead>
        <tr>
            <th>N</th>
            <th>X<sub>i</sub></th>
            <th>Y<sub>i</sub></th>
            <th>X<sup>2</sup></th>
            <th>Y<sup>2</sup></th>
            <th>XY</th>
        </tr>
        </thead>
        <tbody>
        {#each x as item, index}
            <tr>
                <td>{index + 1}</td>
                <td>{x[index]}</td>
                <td>{y[index]}</td>
                <td>{x2[index]}</td>
                <td>{y2[index]}</td>
                <td>{xy[index]}</td>
            </tr>
        {/each}
        <tr>
            <td>&sum;</td>
            <td>{sum.x}</td>
            <td>{sum.y}</td>
            <td>{sum.x2}</td>
            <td>{sum.y2}</td>
            <td>{sum.xy}</td>
        </tr>
        </tbody>
    </table>
</div>

<Scatter {data} {options} />