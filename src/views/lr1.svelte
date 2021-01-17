<script>
    import Bar from "svelte-chartjs/src/Bar.svelte";
    import Line from "svelte-chartjs/src/Line.svelte";

    let randomNumbers = [
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
        ],
        n = randomNumbers.length,
        // кiлькiсть iнтервалiв
        l = Math.ceil(1 + 3.32 * (Math.log10(n))),
        // ширина iнтервала
        stepOfInterval = (randomNumbers[randomNumbers.length - 1] - randomNumbers[0]) / l;

    function getMaxOfArray(numArray) {
        return Math.max.apply(null, numArray);
    }

    // интервалы
    const getNewIntervals = () => {
        let tmp = [];
        let result = [];
        let firstStep = randomNumbers[0];
        let lastStep = randomNumbers[randomNumbers.length - 1] + 1;

        for (let i = 0; i <= l; i++) {
            tmp.push(firstStep);
            firstStep += Math.floor(stepOfInterval);
        }

        tmp.push(lastStep);

        for (let i = 0; i < tmp.length - 1; i++) {
            result.push({
                x1: tmp[i],
                x2: tmp[i + 1]
            });
        }

        return result;
    };

    // Подсчет частоты вхождений
    let calculeteNewFreq = (isRelative = false) => {
        const intervals = getNewIntervals();
        let result = [];

        // подсчет вхождений в интревал
        for (let i = 0, len = intervals.length; i < len; i++) {
            let sum = 0;
            for (let j = 0; j < n; j++) {
                if (randomNumbers[j] >= Math.floor(intervals[i].x1)
                    && randomNumbers[j] < Math.floor(intervals[i].x2)
                ) {
                    sum++;
                }

                if (randomNumbers[j] <= Math.floor(intervals[i].x2)
                    && intervals[intervals.length - 1].x2 === randomNumbers[j]
                ) {
                    sum++;
                }
            }

            if (isRelative) {
                result.push(sum / n);
            } else {
                result.push(sum);
            }
        }

        return result;
    };

    let table = {
            intervals: getNewIntervals(),
            ni: calculeteNewFreq(), // частота
            Wi: calculeteNewFreq(true),  // вiдносна частота
            // накопичувальна частота
            get sumNi() {
                let sum = 0;
                return this.ni.map(item => sum += item);
            },
            // вiдносна накопичувальна частота
            get sumWi() {
                let sum = 0;
                return this.Wi.map(item => sum += item);
            },
            get medianaInterval() {
                return this.intervals.map(item => ((item.x1 + item.x2) / 2))
            }
        },
        empic = [],
        styles = 'min-width: 100px',
        sortElements = (a, b) => a - b,
        moda = () => {
            const intervals = getNewIntervals();
            let max = getMaxOfArray(table.ni);
            let indexOfMax = table.ni.indexOf(max);

            return Math.floor(intervals[indexOfMax].x1 + (Math.floor(stepOfInterval) * ((max - table.ni[indexOfMax - 1]) / ((2 * max) - table.ni[indexOfMax - 1] - table.ni[indexOfMax + 1]))));
        },
        mediana = (randomNumbers[(n / 2) - 1] + randomNumbers[n / 2]) / 2,
        xSer = () => ((randomNumbers.reduce((sum, item) => {
            return sum += item
        }, 0)) / randomNumbers.length).toFixed(1),
        D = () => {
            let average = +xSer();
            let sum = 0;

            for (let i = 0, len = table.medianaInterval.length; i < len; i++) {
                sum += ((table.medianaInterval[i] - average) ** 2) * table.ni[i];
            }

            return (sum / n).toFixed(2);
        },
        Qser = Math.sqrt(D()),
        V = ((Qser / xSer()) * 100).toFixed(0),
        As = () => {
            let sum = 0;
            let average = xSer();

            for (let i = 0, len = table.medianaInterval.length; i < len; i++) {
                sum += ((table.medianaInterval[i] - average) ** 3) * table.ni[i];
            }

            let m3 = (1 / n) * sum;

            return (m3 / (Qser ** 3)).toFixed(3)
        },
        Es = () => {
            let sum = 0;
            let average = xSer();

            for (let i = 0, len = table.medianaInterval.length; i < len; i++) {
                sum += ((table.medianaInterval[i] - average) ** 4) * table.ni[i];
            }

            let m4 = (1 / n) * sum;

            return (m4 / (Qser ** 4) - 3).toFixed(2);
        };

    console.log(randomNumbers[(n / 2) - 1], randomNumbers[n / 2]);

    // сортировка
    randomNumbers = randomNumbers.sort(sortElements);

    // доверительный интервал
    let calculateDovInterval = () => {
        let average = +xSer(),
            t = 2.58,
            q = +Qser,
            x1 = average - (t * (q / Math.sqrt(n))),
            x2 = average + (t * (q / Math.sqrt(n)).toFixed(2));

        return '(' + x1.toFixed(2) + '; ' + x2.toFixed(2) + ')';
    };

    // рендер интервалов
    let generateVariantLists = () => {
        const intervals = getNewIntervals();
        let html = '';

        for (let i = 0, len = intervals.length; i < len; i++) {
            html += `<td style="${styles}">
                        [${intervals[i].x1}; ${intervals[i].x2}${i === len - 1 ? ']' : ')'}
                     </td>`;
        }

        return html;
    };

    // создание графика частот
    let generateEpmGraphic = () => {
        let tmp = [];
        const intervals = getNewIntervals();

        for (let i = 0, j = 1, len = table.sumWi.length; i < len; i++, j++) {
            tmp.push({
                x: `[${intervals[i].x1}; ${intervals[i].x2})`,
                y: table.sumWi[i].toFixed(2)
            });
        }

        return tmp;
    }

    // емпиричная функция
    table.ni.map(item => {
        empic.push(item / n);
    });

    // рендер накопительных частот
    let acumulateFreaqRender = () => {
        let html = '';

        table.sumNi.map(item => {
            html += `<td style="${styles}">${item}</td>`;
        });

        return html;
    };

    // рендер частоты вхождений
    let generateFreq = () => {
        let html = '';

        table.ni.map(item => {
            html += `<td style="${styles}">${item}</td>`;
        })

        return html;
    };

    // рендер относительной частоты
    let generateRelativeFreq = () => {
        let html = '';

        table.Wi.forEach(item => {
            html += `<td style="${styles}">${item.toFixed(2)}</td>`;
        });

        return html;
    };

    // рендер относительной накопительной частоты
    let generateRelativeAcumulateFreaq = () => {
        let html = '';

        table.sumWi.forEach(item => {
            html += `<td style="${styles}">${item.toFixed(2)}</td>`;
        });

        return html;
    };

    // рендер медианы
    let generateMedian = () => {
        let html = '';

        table.medianaInterval.map(item => {
            html += `<td style="${styles}">${item}</td>`;
        });

        return html;
    };

    // чарты
    // относительные
    let barData = {
        labels: table.intervals.map((item, index, arr) => {
            return `[${item.x1}; ${item.x2})`;
        }),
        datasets: [
            {
                data: table.Wi,
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
                data: table.Wi,
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
        labels: table.sumWi.map(item => item.toFixed(2)),
        datasets: [
            {
                data: table.sumWi.map(item => item.toFixed(2)),
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
                data: table.sumWi.map(item => item.toFixed(2)),
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
        labels: table.medianaInterval,
        datasets: [{
            data: table.sumWi.map(item => item.toFixed(2)),
            fill: false,
            steppedLine: true,
            borderColor: "rgb(0, 0, 0)",
            lineTension: 0.1
        }]
    };

    let getTable = () => {
        let html = '<table><tbody><tr>';

        randomNumbers.forEach((item, index) => {
            html += `<td>${item}</td>`;
            if ((index + 1) % 20 === 0 ) {
                html += '</tr><tr>'
            }
        });

        html += '</tr></tbody></table>';

        return html;
    };
</script>

<h2>
    Випадкові числа
</h2>
{@html getTable()}

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
                <p class="card-text text-center">{mediana}</p>
            </div>
        </div>
    </div>
    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Мода:</div>
            <div class="card-body">
                <p class="card-text text-center">{moda()}</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркове середне:</div>
            <div class="card-body">
                <p class="card-text text-center">{xSer()}</p>
            </div>
        </div>
    </div>
    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркова дисперсія:</div>
            <div class="card-body">
                <p class="card-text text-center">{D()}</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркове середнє квадратичне відхилення:</div>
            <div class="card-body">
                <p class="card-text text-center">{Qser.toFixed(2)}</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибірковий коефіцієнт варіації:</div>
            <div class="card-body">
                <p class="card-text text-center">{V}%</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркові коефіцієнти асиметрії:</div>
            <div class="card-body">
                <p class="card-text text-center">{As()}</p>
            </div>
        </div>
    </div>

    <div class="col-md-3">
        <div class="card border-primary mb-3">
            <div class="card-header">Вибіркові коефіцієнти ексцесу:</div>
            <div class="card-body">
                <p class="card-text text-center">{Es()}</p>
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