<script>
    import Bar from "svelte-chartjs/src/Bar.svelte";
    import Line from "svelte-chartjs/src/Line.svelte";

    let randomNumbers = [],
        length = 200,
        relativeFreq = [],
        freaq = [],
        mediana = [],
        acumulateFreaq = [],
        relativeAcumulateFreaq = [],
        empic = [],
        styles = 'min-width: 100px',
        sortElements = (a, b) => a - b;

    function getMaxOfArray(numArray) {
        return Math.max.apply(null, numArray);
    }

    // случайный набор чисел (200)
    /*for (let i = 0; i < length; i++) {
        randomNumbers.push(Math.ceil(Math.random() * (100 - 50) + 50));
    }*/

    randomNumbers = [
        51, 51, 51, 52, 52, 52, 52, 52, 53, 53, 53, 53, 53, 54, 54, 54, 54, 54, 55, 55,
        55, 55, 56, 56, 56, 56, 56, 56, 56, 56, 57, 57, 57, 57, 57, 57, 57, 57, 57, 57,
        58, 58, 58, 58, 58, 59, 59, 59, 60, 60, 60, 61, 61, 61, 61, 61, 62, 62, 63, 64,
        64, 64, 64, 66, 66, 66, 66, 66, 66, 67, 67, 67, 67, 68, 68, 69, 69, 69, 70, 70,
        70, 70, 71, 71, 72, 72, 72, 72, 72, 73, 74, 74, 74, 74, 74, 75, 75, 75, 75, 75,
        76, 76, 76, 77, 77, 77, 78, 78, 79, 79, 79, 79, 79, 80, 80, 80, 80, 81, 81, 81,
        81, 81, 81, 81, 82, 82, 83, 83, 83, 83, 83, 83, 84, 84, 84, 84, 84, 85, 85, 85,
        85, 86, 86, 86, 86, 86, 87, 87, 87, 88, 88, 88, 88, 88, 89, 89, 91, 91, 91, 92,
        92, 93, 93, 93, 93, 94, 94, 94, 94, 94, 94, 95, 95, 95, 95, 95, 96, 96, 96, 96,
        96, 97, 98, 98, 98, 98, 98, 98, 98, 98, 99, 99, 99, 99, 100, 100, 100, 100, 100, 100,
    ];

    /*randomNumbers = [
        3,24,31,46,17,4,14,26,21,27,40,7,28,47,20,15,19,23,27,9,
        21,5,27,22,20,22,45,7,19,30,28,33,28,4,39,17,38,8,23,2,
        35,8,17,6,13,44,5,8,6,25,19,5,10,45,33,34,33,26,33,38,
        21,18,13,26,36,28,41,46,39,4,48,16,28,35,32,16,4,22,11,15,
        5,26,6,23,16,2,22,40,36,37,50,18,43,42,33,3,10,18,19,13,
        46,49,7,31,19,26,48,28,39,49,11,27,1,23,24,30,22,1,20,8,
        20,19,14,47,23,50,13,11,17,16,13,44,40,10,25,46,23,19,33,11,
        2,8,43,2,28,31,16,27,35,35,35,20,6,13,20,9,44,50,47,17,
        10,34,36,46,40,19,33,48,41,30,31,39,43,1,41,37,12,37,12,33,
        16,5,37,6,5,14,19,46,48,8,32,49,35,22,44,4,5,43,15,6,
    ];*/

    // сортировка
    randomNumbers = randomNumbers.sort(sortElements);

    // количество интервалов
    const intervalCount = Math.ceil(1 + 3.32 * (Math.log10(length)));

    let intervalsArr = [];
    for (let i = 1; i <= intervalCount; i++) {
        intervalsArr.push(i);
    }

    // ширина интервала
    const stepOfInterval = (randomNumbers[randomNumbers.length - 1] - randomNumbers[0]) / intervalCount;

    // интервалы
    const getIntervals = () => {
        let arr = [];
        let firstStep = randomNumbers[0];
        let lastStep = randomNumbers[randomNumbers.length - 1];

        for (let i = 0; i <= intervalCount; i++) {
            arr.push(firstStep);
            firstStep += Math.floor(stepOfInterval);
        }

        arr.push(lastStep);

        return arr;
    };

    // рендер интервалов
    let generateVariantLists = () => {
        const intervals = getIntervals();
        let html = '';

        for (let i = 1, len = intervals.length; i < len; i++) {
            html += `<td style="${styles}">
                        [${Math.floor(intervals[i - 1])}; ${Math.floor(intervals[i])}${i === len - 1 ? ']' : ')'}
                     </td>`;
        }

        return html;
    };

    // Подсчет частоты вхождений
    let calculeteFreq = () => {
        const intervals = getIntervals();

        // подсчет вхождений в интревал
        for (let i = 0, len = intervals.length; i < len - 1; i++) {
            let sum = 0;
            for (let j = 0, l = randomNumbers.length; j < l; j++) {
                if (randomNumbers[j] >= Math.floor(intervals[i])
                    && randomNumbers[j] < Math.floor(intervals[i + 1])
                ) {
                    sum++;
                }
                if (randomNumbers[j] <= Math.floor(intervals[i + 1])
                    && intervals[intervals.length - 1] === randomNumbers[j]
                ) {
                    sum++;
                }
            }

            freaq.push(sum);
            relativeFreq.push(sum / length);
        }
    };
    calculeteFreq();

    // подсчет накопительных частот
    freaq.map(item => {
        if (acumulateFreaq.length === 0) {
            acumulateFreaq.push(item);
        } else {
            let tmp = acumulateFreaq[acumulateFreaq.length - 1] + item;
            acumulateFreaq.push(tmp);
        }
    });

    // подсчет относительных накопительных частот
    relativeFreq.map(item => {
        if (relativeAcumulateFreaq.length === 0) {
            relativeAcumulateFreaq.push(item);
        } else {
            let tmp = relativeAcumulateFreaq[relativeAcumulateFreaq.length - 1] + item;
            relativeAcumulateFreaq.push(tmp);
        }
    });

    // создание графика частот
    let generateEpmGraphic = () => {
        let tmp = [];
        const intervals = getIntervals();

        for (let i = 0, j = 1, len = relativeAcumulateFreaq.length; i < len; i++, j++) {
            tmp.push({
                x: `[${Math.floor(intervals[i])}; ${Math.floor(intervals[j])})`,
                y: relativeAcumulateFreaq[i].toFixed(2)
            });
        }

        return tmp;
    }

    // емпиричная функция
    acumulateFreaq.map(item => {
        empic.push(item / length);
    });


    // рендер накопительных частот
    let acumulateFreaqRender = () => {
        let html = '';

        acumulateFreaq.map(item => {
            html += `<td style="${styles}">${item}</td>`;
        });

        return html;
    };

    // рендер частоты вхождений
    let generateFreq = () => {
        let html = '';

        freaq.map(item => {
            html += `<td style="${styles}">${item}</td>`;
        })

        return html;
    };

    // рендер относительной частоты
    let generateRelativeFreq = () => {
        let html = '';

        relativeFreq.forEach(item => {
            html += `<td style="${styles}">${item}</td>`;
        });

        return html;
    };
    // рендер относительной накопительной частоты
    let generateRelativeAcumulateFreaq = () => {
        let html = '';

        relativeAcumulateFreaq.forEach(item => {
            html += `<td style="${styles}">${item.toFixed(2)}</td>`;
        });

        return html;
    };

    let calculateMediana = () => {
        const intervals = getIntervals();

        for (let i = 1, len = intervals.length; i < len; i++) {
            mediana.push(((intervals[i - 1] + intervals[i]) / 2).toFixed(2));
        }
    };

    // рендер медианы
    let generateMedian = () => {
        calculateMediana();
        let html = '';

        mediana.map(item => {
            html += `<td style="${styles}">${item}</td>`;
        });

        return html;
    };

    // moda
    let calculateModa = () => {
        const intervals = getIntervals();
        let max = getMaxOfArray(freaq);
        let indexOfMax = freaq.indexOf(max)

        return Math.ceil(intervals[indexOfMax] + (stepOfInterval * (max - freaq[indexOfMax - 1]) / (2 * max - freaq[indexOfMax - 1] - freaq[indexOfMax + 1])));
    };

    // выборочное среднее
    let calculateSelectedAverage = () => {
        let sum = 0;
        for (let i = 0, len = mediana.length; i < len; i++) {
            sum += mediana[i] * freaq[i];
        }

        return ((1 / length) * sum).toFixed(2);
    };

    // дисперсия
    let calculateDisp = () => {
        let average = calculateSelectedAverage();
        let sum = 0;

        for (let i = 0, len = mediana.length; i < len; i++) {
            //sum += ((mediana[i] - average)**2) * freaq[i];
            sum += (freaq[i] * mediana[i] ** 2);
        }

        return ((1 / length) * sum - (average) ** 2).toFixed(2);
    };

    // квадратическое отклонение
    let calculateSquareDisp = () => {
        return (Math.sqrt(calculateDisp())).toFixed(2);
    };

    // коефициент вариации
    let calculateV = () => {
        return ((calculateSquareDisp() / calculateSelectedAverage()) * 100).toFixed(0);
    }

    // вибіркові коефіцієнти асиметрії
    let calculateAs = () => {
        let sum = 0;
        let average = calculateSelectedAverage();

        for (let i = 0, len = mediana.length; i < len; i++) {
            sum += ((mediana[i] - average) ** 3) * freaq[i];
        }

        let m3 = (1 / length) * sum;

        return (m3 / (calculateSquareDisp() ** 3)).toFixed(3);
    };

    // eксцес
    let calculateEs = () => {
        let sum = 0;
        let average = calculateSelectedAverage();

        for (let i = 0, len = mediana.length; i < len; i++) {
            sum += ((mediana[i] - average) ** 4) * freaq[i];
        }

        let m4 = (1 / length) * sum;

        return (m4 / (calculateSquareDisp() ** 4) - 3).toFixed(2);
    };

    // доверительный интервал
    let calculateDovInterval = () => {
        let average = +calculateSelectedAverage();
        let t = 2.58;
        let q = +calculateSquareDisp();
        let x1 = average - (t * (q / Math.sqrt(length)));
        let x2 = average + (t * (q / Math.sqrt(length)).toFixed(2));

        return '(' + x1.toFixed(2) + '; ' + x2.toFixed(2) + ')';
    };

    // чарты
    // относительные
    let barData = {
        labels: getIntervals().map((item, index, arr) => {
            return arr[index + 1] ? `[${Math.floor(arr[index])}; ${Math.floor(arr[index + 1])})` : '';
        }),
        datasets: [
            {
                data: relativeFreq,
                fill: false,
                lineTension: 0,
                type: 'line',
                backgroundColor: "rgb(248,9,45)",
                borderColor: "rgb(255,0,36)",
                borderCapStyle: "butt",
                borderDash: [],
                borderDashOffset: 0.0,
                borderJoinStyle: "miter",
                pointBorderColor: "rgb(249 3 3)",
                pointBackgroundColor: "rgb(249 3 3)",
                pointBorderWidth: 5,
                pointHoverRadius: 5,
                pointHoverBackgroundColor: "rgb(0, 0, 0)",
                pointHoverBorderColor: "rgba(220, 220, 220,1)",
                pointHoverBorderWidth: 2,
                pointRadius: 1,
                pointHitRadius: 10,
            },
            {
                label: "відноснa частотa",
                data: relativeFreq,
                backgroundColor: "rgb(82,117,224)",
                borderWidth: 1,
                borderColor: "rgb(6,63,232)",
            }
        ]
    };
    let barOptions = {
        responsive: true,
        scales: {
            xAxes: [{
                type: 'category',

                // Specific to Bar Controller
                categoryPercentage: 1.0,
                barPercentage: 1.0,

                gridLines: {
                    offsetGridLines: true
                }
            }],
            yAxes: [{
                type: 'linear',
                ticks: {
                    suggestedMin: 0,
                    beginAtZero: true
                }
            }]
        },
        dataset: {
            xAxes: [
                {
                    gridLines: {
                        display: false,
                        color: "rgba(0, 0, 0, 0.1)"
                    }
                }
            ],
            yAxes: [
                {
                    gridLines: {
                        display: false,
                        color: "rgba(0, 0, 0, 0.1)"
                    },
                    ticks: {
                        suggestedMin: 0,
                        beginAtZero: true
                    }
                }
            ]
        },
        legend: {
            display: false,
        }
    };

    // накопительные
    let barDataAcum = {
        labels: acumulateFreaq,
        datasets: [
            {
                data: acumulateFreaq,
                fill: false,
                lineTension: 0,
                type: 'line',
                backgroundColor: "rgb(249 3 3)",
                borderColor: "rgb(249 3 3)",
                borderCapStyle: "butt",
                borderDash: [],
                borderDashOffset: 0.0,
                borderJoinStyle: "miter",
                pointBorderColor: "rgb(249 3 3)",
                pointBackgroundColor: "rgb(249 3 3)",
                pointBorderWidth: 5,
                pointHoverRadius: 5,
                pointHoverBackgroundColor: "rgb(0, 0, 0)",
                pointHoverBorderColor: "rgba(220, 220, 220,1)",
                pointHoverBorderWidth: 2,
                pointRadius: 1,
                pointHitRadius: 10,
            },
            {
                label: "Накопичені відносні частоти",
                data: acumulateFreaq,
                backgroundColor: "rgb(15,204,229)",
                borderWidth: 1,
                borderColor: "rgb(0,221,255)"
            }
        ]
    };
    let barOptionsAcum = {
        responsive: true,
        scales: {
            xAxes: [{
                type: 'category',

                // Specific to Bar Controller
                categoryPercentage: 1.0,
                barPercentage: 1.0,

                gridLines: {
                    offsetGridLines: true
                }
            }],
            yAxes: [{
                type: 'linear',
                ticks: {
                    suggestedMin: 0,
                    beginAtZero: true
                }
            }]
        },
        dataset: {
            xAxes: [
                {
                    gridLines: {
                        display: false,
                        color: "rgba(0, 0, 0, 0.1)"
                    }
                }
            ],
            yAxes: [
                {
                    gridLines: {
                        display: false,
                        color: "rgba(0, 0, 0, 0.1)"
                    },
                    ticks: {
                        beginAtZero: true
                    }
                }
            ]
        },
        legend: {
            display: false,
        }
    };

    let scatterData = {
        labels: mediana,
        datasets: [{
            data: relativeAcumulateFreaq.map(item => item.toFixed(2)),
            fill: false,
            steppedLine: true,
            borderColor: "rgb(0, 0, 0)",
            lineTension: 0.1
        }]
    };
</script>

<h2>
    Випадкові числа
</h2>
{#each randomNumbers.sort(sortElements) as number, index}
    {number}
    {#if (index + 1) % 20 === 0 }
        {@html '<br/>'}
    {/if}
{/each}

<h2 class="mt-5">Таблиця частот</h2>
<div class="table-responsive mb-5">
    <table class="table">
        <tbody>
        <tr>
            <th>інтервали</th>
            {@html generateVariantLists()}
        </tr>
        <tr>
            <th>Частоти</th>
            {@html generateFreq()}
        </tr>
        <tr>
            <th>Накопичувальні частоти</th>
            {@html acumulateFreaqRender()}
        </tr>
        <tr>
            <th>Відносні частоти</th>
            {@html generateRelativeFreq()}
        </tr>
        <tr>
            <th>Відносні накопичувальні частоти</th>
            {@html generateRelativeAcumulateFreaq()}
        </tr>
        <tr>
            <th>Медіана інтервала</th>
            {@html generateMedian()}
        </tr>
        </tbody>
    </table>
</div>

<h2>Гістограма і полігон відносних частот</h2>
<Bar class="mb-5" data={barData} options={barOptions}/>

<h2>Гістограма і полігон накопичених відносних частот</h2>
<Bar class="mb-5" data={barDataAcum} options={barOptionsAcum}/>

<h2>Графік эмпирической функции</h2>
<Line class="mb-5" data={scatterData} options={{
        responsive: true,
        legend: {
            display: false,
        },
        scales: {
            yAxes: [{
                ticks: {
                    suggestedMin: 0,
                    beginAtZero: true
                }
            }]
        },
    }}/>

<div class="row">
    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Медіана:</div>
            <div class="card-body">
                <p class="card-text text-center">{(randomNumbers[(length / 2) - 1] + randomNumbers[length / 2]) / 2}</p>
            </div>
        </div>
    </div>
    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Мода:</div>
            <div class="card-body">
                <p class="card-text text-center">{calculateModa()}</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркове середне:</div>
            <div class="card-body">
                <p class="card-text text-center">{calculateSelectedAverage()}</p>
            </div>
        </div>
    </div>
    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркова дисперсія:</div>
            <div class="card-body">
                <p class="card-text text-center">{calculateDisp()}</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркове середнє квадратичне відхилення:</div>
            <div class="card-body">
                <p class="card-text text-center">{calculateSquareDisp()}</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибірковий коефіцієнт варіації:</div>
            <div class="card-body">
                <p class="card-text text-center">{calculateV()}%</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркові коефіцієнти асиметрії:</div>
            <div class="card-body">
                <p class="card-text text-center">{calculateAs()}</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркові коефіцієнти ексцесу:</div>
            <div class="card-body">
                <p class="card-text text-center">{calculateEs()}</p>
            </div>
        </div>
    </div>
    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Довірчий інтервал для математичного сподівання з надійністю 0.99:</div>
            <div class="card-body">
                <p class="card-text text-center">{calculateDovInterval()}</p>
            </div>
        </div>
    </div>
</div>