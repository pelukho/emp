<script>
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

    function getMaxOfArray(numArray) {
        return Math.max.apply(null, numArray);
    }

    let length = randomNumbers.length,
        sum = table.ni.reduce((s, i) => {return s+=i}, 0),
        sum1 = 0,
        sum2 = 0,
        xx = 0;

    let generateDataForRows = () => {
        let data = [];
        let intervals = getNewIntervals();
        let average = xSer();
        let Q = Math.sqrt(D());
        let alfa = 0.01;
        let f = [-0.450, -0.406, -0.338, -0.242, -0.125, 0.004, 0.136, 0.251, 0.343, 0.409, 0.453];

        for (let i = 1, len = intervals.length; i < len; i++) {
            let vars = {
                x1: intervals[i].x1,
                x2: intervals[i].x2,
                n: table.ni[i],
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

            sum1 += vars.n4;
            sum2 += vars.n6;
            xx += (vars.n ** 2 ) / vars.n1;

            data.push(vars);
        }

        return data;

    };
    let k = (getNewIntervals().length -1) - 2 - 1;
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

