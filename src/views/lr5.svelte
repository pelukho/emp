<script>
    import Scatter from 'svelte-chartjs/src/Scatter.svelte';

    let x = [68, 70, 73, 75, 81, 88, 92, 94],
        y = [8, 8.5, 9, 9.5, 10, 10.5, 11.5, 11],
        x2 = x.map(item => item ** 2),
        y2 = y.map(item => item ** 2),
        xy = x.map((item, index) => item * y[index]),
        valueFromTable = 2.45,
        sum = {
            x: x.reduce((sum, item) => {
                return sum += item
            }, 0),
            y: y.reduce((sum, item) => {
                return sum += item
            }, 0),
            x2: x2.reduce((sum, item) => {
                return sum += item
            }, 0),
            y2: y2.reduce((sum, item) => {
                return sum += item
            }, 0),
            xy: xy.reduce((sum, item) => {
                return sum += item
            }, 0),
        },
        alfa = 0.05,
        n = x.length,
        rxy = (n * sum.xy - (sum.x * sum.y)) / Math.sqrt(((n * sum.x2) - (sum.x ** 2)) * ((n * sum.y2) - (sum.y ** 2))),
        a0 = ((sum.y * sum.x2) - (sum.xy * sum.x)) / ((n * sum.x2) - (sum.x ** 2)),
        a1 = ((n * sum.xy) - (sum.x * sum.y)) / ((n * sum.x2) - (sum.x ** 2)),
        b0 = ((sum.x * sum.y2) - (sum.xy * sum.y)) / ((n * sum.y2) - (sum.y ** 2)),
        b1 = ((n * sum.xy) - (sum.x * sum.y)) / ((sum.y2 * n) - (sum.y ** 2)),
        checkAB = Math.sqrt(a1 * b1),
        xline = y.map(item => {
            return {
                x: b0 + (b1 * item),
                y: item
            };
        }),
        yline = y.map(item => {
            return {
                x: (a0 + (a1 * item)),
                y: item
            };
        }),
        tr = rxy * Math.sqrt((n - 2) / (1 - (rxy ** 2))),
        t2cr = `(${alfa}; ${n - 2})`

    let data = {
        datasets: [
            {
                label: 'Кореляційне поле x',
                data: x.map((item, index) => {
                    return {x: item, y: y[index]}
                }),
                backgroundColor: '#FF6989',
                borderColor: '#f81946',
            },
            {
                label: 'Кореляційне поле y',
                data: y.map((item, index) => {
                    return {x: item, y: x[index]}
                }),
                backgroundColor: '#ff69f0',
                borderColor: '#f81981',
            },
            {
                type: 'line',
                label: `x = ${b0.toFixed(2)} + ${b1.toFixed(2)}y`,
                borderColor: '#0447fa',
                borderWidth: 2,
                fill: false,
                data: xline
            },
            {
                type: 'line',
                label: `y = ${a0.toFixed(2)} + ${a1.toFixed(2)}x`,
                borderColor: '#5afa04',
                borderWidth: 2,
                fill: false,
                data: yline
            }
        ]
    };

    let options = {
        scales: {
            xAxes: [{
                type: 'linear',
                ticks: {
                    min: 0,
                    //max: 10
                }
            }],
            yAxes: [{
                type: 'linear',
                ticks: {
                    min: 0,
                    // max: 10
                }
            }]
        }
    }
</script>

<h2 class="text-center">Розрахункова таблиця</h2>
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

<h2 class="text-center">
    Робимо розрахункови
</h2>
<div class="row mb-5">
    <div class="col-md-4">
        <div class="card  bg-light mb-3">
            <div class="card-header">r <sub>X,Y</sub></div>
            <div class="card-body">
                <h4 class="card-title">{rxy.toFixed(2)}</h4>
            </div>
        </div>
    </div>

    <div class="col-md-4">
        <div class="card  bg-light mb-3">
            <div class="card-header">a<sub>0</sub></div>
            <div class="card-body">
                <h4 class="card-title">{a0.toFixed(2)}</h4>
            </div>
        </div>
    </div>

    <div class="col-md-4">
        <div class="card  bg-light mb-3">
            <div class="card-header">a<sub>1</sub></div>
            <div class="card-body">
                <h4 class="card-title">{a1.toFixed(2)}</h4>
            </div>
        </div>
    </div>

    <div class="col-md-4">
        <div class="card  bg-light mb-3">
            <div class="card-header">b<sub>0</sub></div>
            <div class="card-body">
                <h4 class="card-title">{b0.toFixed(2)}</h4>
            </div>
        </div>
    </div>

    <div class="col-md-4">
        <div class="card  bg-light mb-3">
            <div class="card-header">b<sub>1</sub></div>
            <div class="card-body">
                <h4 class="card-title">{b1.toFixed(2)}</h4>
            </div>
        </div>
    </div>

    <div class="col-md-4">
        <div class="card  bg-light mb-3">
            <div class="card-header">T<sub>r</sub></div>
            <div class="card-body">
                <h4 class="card-title">{tr.toFixed(2)}</h4>
            </div>
        </div>
    </div>

    <div class="col-md-4">
        <div class="card  bg-light mb-3">
            <div class="card-header">t<sub>2cr</sub></div>
            <div class="card-body">
                <h4 class="card-title">{t2cr} = {valueFromTable}</h4>
            </div>
        </div>
    </div>
</div>


<h2 class="text-center">Кореляційне поле та лінії регресії</h2>
<div class="mb-5">
    <Scatter {data} {options}/>
</div>

<div class="row">
    <div class="col-md-12">
        <div class="card border-primary mb-3">
            <div class="card-header">Перевірка гіпотез про значущість коефіцієнта кореляції</div>
            <div class="card-body">
                <p class="card-text">Попередній результат r<sub>XY</sub> = {rxy.toFixed(2)} є достатньо очевидним. </p>
                <p class="card-text">Проте, якщо |r<sub>X,Y</sub>| близько до 0,5 або ще менше, ми не зможемо сказати, що Х та У кореляційнопов’язані.</p>
                <p class="card-text">
                    Якщо r<sub>X,Y</sub> не дорівнює 0, то це не значить, що генеральний коефіцієнт кореляції теж не дорівнює 0. </p>
                <p class="card-text">
                    Необхідно перевірити основну гіпотезу: відхилення r<sub>X,Y</sub> від 0 не значуще та випадкове, тобто
                    H<sub>0</sub> : r<sub>X,Y</sub> = 0 <br>
                    Альтернативна гіпотеза H<sub>1</sub> : r<sub>X,Y</sub> != 0
                </p>
            </div>
        </div>
    </div>
</div>

<div class="row">
    <div class="col-md-12">
        <div class="card  {tr < t2cr ? 'border-primary' : 'border-danger'} mb-3">
            <div class="card-header">
                <h2 class="text-center">Висновок</h2>
            </div>
            <div class="card-body">
                <p class="card-text">
                    {#if tr < t2cr}
                        При заданому рівні значущості alfa |T<sub>r</sub>| &lt; t<sub>2.cr</sub>, приймається нульова гіпотеза
                    {:else}
                        При заданому рівні значущості alfa |T<sub>r</sub>| &gt; t<sub>2.cr</sub>, приймається альтернативна гіпотеза
                    {/if}
                </p>
            </div>
        </div>
    </div>
</div>