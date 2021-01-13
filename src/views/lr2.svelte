<script>
    let randomNumbers = [],
        length = 200,
        relativeFreq = [],
        freaq = [],
        mediana = [],
        acumulateFreaq = [],
        relativeAcumulateFreaq = [],
        empic = [],
        sum = 0,
        sum1 = 0,
        sum2 = 0,
        xx = 0,
        sortElements = (a, b) => a - b;

    function getMaxOfArray(numArray) {
        return Math.max.apply(null, numArray);
    }

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
        let lastStep = randomNumbers[randomNumbers.length - 1] + 1;

        for (let i = 0; i <= intervalCount; i++) {
            arr.push(firstStep);
            firstStep += Math.floor(stepOfInterval);
        }

        arr.push(lastStep);

        return arr;
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

    let calculateMediana = () => {
        const intervals = getIntervals();

        for (let i = 1, len = intervals.length; i < len; i++) {
            mediana.push(((intervals[i - 1] + intervals[i]) / 2).toFixed(2));
        }
    };
    calculateMediana();

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

    let generateDataForRows = () => {
        let data = [];
        let intervals = getIntervals();
        let average = calculateSelectedAverage();
        let Q = calculateSquareDisp();
        let alfa = 0.01;
        let f = [-0.450, -0.406, -0.338, -0.242, -0.125, 0.004, 0.136, 0.251, 0.343, 0.409, 0.453];

        for (let i = 1, len = intervals.length; i < len; i++) {
            let vars = {
                x1: intervals[i-1],
                x2: intervals[i],
                n: freaq[i-1],
                get z() {
                    return ((this.x1 - average) / Q).toFixed(2);
                },
                get z1() {
                   return ((this.x2 - average) / Q).toFixed(2)
                },
                f: f[i-1],
                f1: f[i],
                get n1() {
                    return Math.ceil(length * (this.f1 - this.f));
                },
                get n2(){
                    return this.n - this.n1
                },
                get n3() {
                    return this.n2 ** 2
                },
                get n4() {
                  return +(this.n3 / this.n1).toFixed(2)
                } ,
                get n5() {
                    return this.n ** 2
                },
                get n6() {
                    return +(this.n5 / this.n1).toFixed(2)
                }
            };

            sum += vars.n;
            sum1 += vars.n4;
            sum2 += vars.n6;
            xx += (vars.n ** 2 ) / vars.n1;

            data.push(vars);
        }

        return data;

    };
    let k = (getIntervals().length -1) - 2 - 1;
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

<br>

<div class="table-responsive mb-5">
    <table class="table">
        <thead>
            <tr>
                <th>x<sub>i</sub></th>
                <th>x<sub>i</sub> + 1</th>
                <th>n<sub>i</sub><sup>*</sup></th>
                <th>z<sub>i</sub></th>
                <th>z<sub>i</sub> + 1</th>
                <th>Фz<sub>i</sub></th>
                <th>Ф(z<sub>i</sub> + 1)</th>
                <th>np<sub>i</sub></th>
                <th>n<sup>*</sup><sub>i</sub> - np<sub>i</sub></th>
                <th>(n<sup>*</sup><sub>i</sub> - np<sub>i</sub>)<sup>2</sup></th>
                <th>(n<sup>*</sup><sub>i</sub> - np<sub>i</sub>)<sup>2</sup>/np<sub>i</sub></th>
                <th>n<sub>i</sub><sup>*2</sup></th>
                <th>n<sub>i</sub><sup>*2</sup>/n<sub>i</sub><sup>'</sup></th>
            </tr>
        </thead>
        <tbody>
        {#each generateDataForRows() as row}
            <tr>
                <td>{row.x1}</td>
                <td>{row.x2}</td>
                <td>{row.n}</td>
                <td>{row.z}</td>
                <td>{row.z1}</td>
                <td>{row.f}</td>
                <td>{row.f1}</td>
                <td>{row.n1}</td>
                <td>{row.n2}</td>
                <td>{row.n3}</td>
                <td>{row.n4}</td>
                <td>{row.n5}</td>
                <td>{row.n6}</td>
            </tr>
        {/each}
        <tr>
            <td>{sum}</td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
            <td>{sum1.toFixed(2)}</td>
            <td></td>
            <td>{sum2.toFixed(2)}</td>
        </tr>
        </tbody>
    </table>
</div>

<div class="row">
    <div class="col-md-12">
        <div class="card text-white {sum2 - sum === sum1 ? 'bg-success' : 'bg-danger'} mb-3">
            <div class="card-header">Перевірка результату:</div>
            <div class="card-body">
                <p class="card-text">
                    {sum2 - sum === sum1 ? "сходиться" : "не сходиться"} <br>
                    X(кр)<sup>2</sup>(0,01;{k}) = 18,5 <br>
                    X(сп)<sup>2</sup> = {sum1.toFixed(2)} <br>

                    {@html sum1 > 0 && sum1 <= 18.5
                        ? 'X(сп)<sup>2</sup> належить до (0; 18.5) <br> гіпотеза про нормальний закон розподілу генеральної сукупності ознаки X приймається'
                        : 'X(сп)<sup>2</sup> не належить до (0; 18.5) <br> гіпотеза про нормальний закон розподілу генеральної сукупності ознаки X не приймається'}
                </p>
            </div>
        </div>
    </div>
</div>

