<script>

    let numberOfList = 10,
        courses = ['курси', 'бакалавр', 'магістр'],
        man = [
            {
                value: [numberOfList, 2 * numberOfList, 2 * numberOfList]
            },
            {
                value: [6 * numberOfList, 3 * numberOfList, numberOfList]
            },
            {
                value: [5 * numberOfList, 2 * numberOfList, 4 * numberOfList]
            }
        ],
        women = [
            {
                value: [3 * numberOfList, numberOfList, numberOfList]
            },
            {
                value: [4 * numberOfList, 5 * numberOfList, 3 * numberOfList]
            },
            {
                value: [2 * numberOfList, 3 * numberOfList, 2 * numberOfList]
            }
        ],
        // сума з кожного роду занять
        sum = {
            sumMen: [
                man[0].value.reduce((s, item) => { return s += item }, 0),
                man[1].value.reduce((s, item) => { return s += item }, 0),
                man[2].value.reduce((s, item) => { return s += item }, 0)
            ],
            sumWomen: [
                women[0].value.reduce((s, item) => { return s += item }, 0),
                women[1].value.reduce((s, item) => { return s += item }, 0),
                women[2].value.reduce((s, item) => { return s += item }, 0)
            ]
        },
        // сума з кожного роду занять у квадрати
        sum2 = {
            sumMen: [
                man[0].value.reduce((s2, item) => { return s2 += (item ** 2) }, 0),
                man[1].value.reduce((s2, item) => { return s2 += (item ** 2) }, 0),
                man[2].value.reduce((s2, item) => { return s2 += (item ** 2) }, 0)
            ],
            sumWomen: [
                women[0].value.reduce((s2, item) => { return s2 += (item ** 2)}, 0),
                women[1].value.reduce((s2, item) => { return s2 += (item ** 2) }, 0),
                women[2].value.reduce((s2, item) => { return s2 += (item ** 2) }, 0)
            ]
        },
        // сумма по фактору B
        sumQual = [sum.sumMen[0] + sum.sumWomen[0], sum.sumMen[1] + sum.sumWomen[1], sum.sumMen[2] + sum.sumWomen[2]],
        // сумма по фактору А
        sumSex = [sum.sumMen.reduce((s, i) => s += i), sum.sumWomen.reduce((s, i) => s += i)],
        // Кількість суб’єктів у кожній групі
        n = courses.length,
        a = sumSex.length,
        b = courses.length,
        N = courses.length * (man.length + women.length),
        sumAB = sum.sumMen[0] + sum.sumMen[1] + sum.sumMen[2] + sum.sumWomen[0] + sum.sumWomen[1] + sum.sumWomen[2],
        Exi2 = sumAB ** 2,
        constOfDispers = (Exi2 / N).toFixed(2),
        Ex2 = sum2.sumMen[0] + sum2.sumMen[1] + sum2.sumMen[2] + sum2.sumWomen[0] + sum2.sumWomen[1] + sum2.sumWomen[2],
        Et2a = (sumSex[0] ** 2) + (sumSex[1] ** 2),
        Et2b = (sumQual[0] ** 2) + (sumQual[1] ** 2) + (sumQual[2] ** 2),
        Et2ab = (sum.sumMen[0] ** 2) + (sum.sumMen[1] ** 2) + (sum.sumMen[2] ** 2) + (sum.sumWomen[0] ** 2) + (sum.sumWomen[1] ** 2) + (sum.sumWomen[2] ** 2),
        SSa = ((1 / (n * b)) * (Et2a)) - (constOfDispers),
        SSb = ((1 / (n * a)) * (Et2b)) - (constOfDispers),
        SSab = (((1 / n) * Et2ab) - ((1 / N) * (Exi2))) - SSa - SSb,
        SSall = Ex2 - (constOfDispers),
        SSvip = SSall - SSa - SSb - SSab,
        dfa = a - 1,
        dfb = b - 1,
        dfab = dfa * dfb,
        dfall = N - 1,
        dfvip = dfall - dfa - dfb - dfab,
        MSa = SSa / dfa,
        MSb = SSb / dfb,
        MSab = SSab / dfab,
        MSvip = SSvip / dfvip,
        Fa = MSa / MSvip,
        Fb = MSb / MSvip,
        Fab = MSab / MSvip,
        Fcritical2 = 6.93;


    let s = 0;
    man[0].value.map((item) => s += (item ** 2))
    console.log(sum2.sumMen[0])
</script>

<style>
    .fixed-width td:first-child {
        width: 10%;
        text-align: center;
    }

    .fixed-width td:nth-child(2),
    .fixed-width td:nth-child(3),
    .fixed-width td:nth-child(4) {
        width: 30%;
        text-align: center;
        vertical-align: middle;
    }
</style>

<div class="table-responsive mb-5">
    <table class="table table-bordered">
        <thead>
        <tr>
            <th></th>
            <th colspan="8" class="text-center">Заробітна плата, тисяч умовних одиниць</th>
            <th rowspan="2" class="text-center align-middle">&sum; по фактору В(квалiфiкацiя)</th>
        </tr>
        <tr>
            <th></th>
            <th colspan="4" class="text-center">Чоловіки</th>
            <th colspan="4" class="text-center">Жінки</th>
        </tr>
        </thead>
        <tbody>
        {#each courses as item, i}
            <tr>
                <td>
                    {item}
                </td>
                {#each man[i].value as sal}
                    <td>{sal}</td>
                {/each}
                <td class="table-danger text-center">{sum.sumMen[i]}</td>
                {#each women[i].value as sal}
                    <td>{sal}</td>
                {/each}
                <td class="table-danger text-center">{sum.sumWomen[i]}</td>
                <td class="text-center table-info">{sumQual[i]}</td>
            </tr>
        {/each}

        <tr>
            <td>&sum; по фактору А (рiд)</td>
            <td colspan="4" class="table-danger text-center">{sumSex[0]}</td>
            <td colspan="4" class="table-danger text-center">{sumSex[1]}</td>
            <td colspan="4" class="table-info text-center">{sumAB}</td>
        </tr>
        </tbody>
    </table>
</div>

<blockquote class="blockquote">
    <p class="mb-0">
        Сформулюємо гіпотези (вплив фактора А без В, вплив фактора В без А, вплив
        взаємодії факторів А і В):
    </p>
    <ol>
        <li>Н0 – Різниця в обсязі відтворення слів за рахунок впливу фактора А між
            групами не істотна, випадкова<br>
            Н1 – Різниця в обсязі відтворення слів за рахунок впливу фактора А між
            групами не є випадковою.
        </li>
        <li>Н0 – Різниця в обсязі відтворення слів за рахунок впливу фактора В між
            групами не істотна, випадкова<br>
            Н1 – Різниця в обсязі відтворення слів за рахунок впливу фактора В між
            групами не є випадковою.
        </li>
        <li> Н0 – Вплив фактора А на обсяг відтворення слів однакова для різних
            градацій фактора В та навпаки<br>
            Н1 – Вплив фактора А на обсяг відтворення слів не є однаковою для
            різних градацій фактора В та навпаки.
        </li>
    </ol>
</blockquote>

<h2 class="text-center">Величини, які необхідні для розрахунку</h2>
<div class="table-responsive mb-5">
    <table class="table table-bordered">
        <thead>
        <tr>
            <th class="text-center">Позначення</th>
            <th class="text-center">Визначення</th>
            <th class="text-center">Розрахунки</th>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td class="text-center">T(A)</td>
            <td class="text-center">Суми по градаціям А</td>
            <td class="text-center">{sumSex[0]}; {sumSex[1]}</td>
        </tr>
        <tr>
            <td class="text-center">&sum;T<sup>2</sup>A</td>
            <td class="text-center">Сума квадратів цих сум</td>
            <td class="text-center">
                {sumSex[0]}<sup>2</sup> + {sumSex[1]}<sup>2</sup> = {Et2a}
            </td>
        </tr>
        <tr>
            <td class="text-center">T(B)</td>
            <td class="text-center">Суми по градаціям В</td>
            <td class="text-center">{sumQual[0]}; {sumQual[1]}; {sumQual[2]}</td>
        </tr>
        <tr>
            <td class="text-center">&sum;T<sup>2</sup>B</td>
            <td class="text-center">Сума квадратів цих сум</td>
            <td class="text-center">
                {sumQual[0]}<sup>2</sup> + {sumQual[1]}<sup>2</sup> + {sumQual[2]}<sup>2</sup> =
                {(sumQual[0] ** 2) + (sumQual[1] ** 2) + (sumQual[2] ** 2)}
            </td>
        </tr>
        <tr>
            <td class="text-center">T(A, B)</td>
            <td class="text-center">Суми по клітинкам</td>
            <td class="text-center">
                {sum.sumMen[0]};
                {sum.sumMen[1]};
                {sum.sumMen[2]};
                {sum.sumWomen[0]};
                {sum.sumWomen[1]};
                {sum.sumWomen[2]}
            </td>
        </tr>
        <tr>
            <td class="text-center">&sum;T<sup>2</sup>AB</td>
            <td class="text-center">Сума квадратів цих сум</td>
            <td class="text-center">
                {sum.sumMen[0]}<sup>2</sup> +
                {sum.sumMen[1]}<sup>2</sup> +
                {sum.sumMen[2]}<sup>2</sup> +
                {sum.sumWomen[0]}<sup>2</sup> +
                {sum.sumWomen[1]}<sup>2</sup> +
                {sum.sumWomen[2]}<sup>2</sup> =
                {Et2ab}
            </td>
        </tr>
        <tr>
            <td class="text-center">n</td>
            <td class="text-center">Кількість суб’єктів у кожній групі</td>
            <td class="text-center">{n}</td>
        </tr>
        <tr>
            <td class="text-center">a</td>
            <td class="text-center">Кількість градацій фактора А</td>
            <td class="text-center">{a}</td>
        </tr>
        <tr>
            <td class="text-center">b</td>
            <td class="text-center">Кількість градацій фактора B</td>
            <td class="text-center">{b}</td>
        </tr>
        <tr>
            <td class="text-center">N</td>
            <td class="text-center">Загальна кількість індивідуальних значень</td>
            <td class="text-center">{N}</td>
        </tr>
        <tr>
            <td class="text-center">&sum;x<sub>i</sub></td>
            <td class="text-center">Сума індивідуальних значень</td>
            <td class="text-center">{sumAB}</td>
        </tr>
        <tr>
            <td class="text-center">(&sum;x<sub>i</sub>)<sup>2</sup></td>
            <td class="text-center">Квадрат цієї суми</td>
            <td class="text-center">{sumAB ** 2}</td>
        </tr>
        <tr>
            <td class="text-center">1/N(&sum;x<sub>i</sub>)<sup>2</sup></td>
            <td class="text-center">Константа, яка віднімається від всіх дисперсій</td>
            <td class="text-center">{constOfDispers}</td>
        </tr>
        <tr>
            <td class="text-center">&sum;x<sub>i</sub><sup>2</sup></td>
            <td class="text-center">Сума квадратів індивідуальних значень</td>
            <td class="text-center">{Ex2}</td>
        </tr>
        </tbody>
    </table>
</div>
<h2 class="text-center">Послідовність операцій у двофакторному дисперсійному аналізі</h2>
<div class="table-responsive mb-5">
    <table class="table table-bordered fixed-width">
        <thead>
        <tr>
            <td>#</td>
            <td>Операція</td>
            <td>Формула</td>
            <td>Розрахунок</td>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td>1</td>
            <td>Порахувати SS<sub>A</sub></td>
            <td><img src="./img/1.png" alt=""></td>
            <td>{SSa.toFixed(2)}</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Порахувати SS<sub>B</sub></td>
            <td><img src="./img/2.png" alt=""></td>
            <td>{SSb.toFixed(2)}</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Порахувати SS<sub>AB</sub></td>
            <td><img src="./img/3.png" alt=""></td>
            <td>{SSab.toFixed(2)}</td>
        </tr>
        <tr>
            <td>4</td>
            <td>Порахувати SS<sub>заг</sub></td>
            <td><img src="./img/4.png" alt=""></td>
            <td>{SSall.toFixed(2)}</td>
        </tr>
        <tr>
            <td>5</td>
            <td>Порахувати SS<sub>вип</sub></td>
            <td><img src="./img/5.png" alt=""></td>
            <td>{SSvip.toFixed(2)}</td>
        </tr>
        <tr>
            <td>6</td>
            <td>Порахувати число степенів вільності</td>
            <td><img src="./img/6.png" alt=""></td>
            <td>
                df<sub>a</sub> = {dfa} <br>
                df<sub>b</sub> = {dfb} <br>
                df<sub>ab</sub> = {dfab} <br>
                df<sub>заг</sub> = {dfall} <br>
                df<sub>вип</sub> = {dfvip}
            </td>
        </tr>
        <tr>
            <td>7</td>
            <td>Розділимо кожну дисперсію на відповідне число п.6</td>
            <td><img src="./img/7.png" alt=""></td>
            <td>
                MS<sub>A</sub> = {MSa.toFixed(2)} <br>
                MS<sub>B</sub> = {MSb.toFixed(2)} <br>
                MS<sub>AB</sub> = {MSab.toFixed(2)} <br>
                MS<sub>вип</sub> = {MSvip.toFixed(2)}
            </td>
        </tr>
        <tr>
            <td>8</td>
            <td>Знайдемо F<sub>емп</sub></td>
            <td><img src="./img/8.png" alt=""></td>
            <td>
                F<sub>A</sub> = {Fa.toFixed(2)} <br>
                F<sub>B</sub> = {Fb.toFixed(2)} <br>
                F<sub>AB</sub> = {Fab.toFixed(2)} <br>
            </td>
        </tr>
        <tr>
            <td>9</td>
            <td>По таблиці Критичні значення критерія Фішера знайдемо F<sub>крит</sub></td>
            <td>Точка перетину df<sub>1</sub> = {dfab}, df<sub>2</sub> = {dfvip}</td>
            <td>
                F<sub>крит({dfab};{dfvip})</sub> = <br>
                <label>
                    (&#11393; &leq; 0,01)
                    <input type="number" class="form-control" bind:value={Fcritical2}/>
                </label>
            </td>
        </tr>
        <tr>
            <td>10</td>
            <td>Порівняти F<sub>емп</sub> та F<sub>крит</sub></td>
            <td><img src="./img/10.png" alt=""></td>
            <td>
                {#if Fa < Fcritical2}
                    F<sub>A</sub> &lt; F<sub>крит</sub> - H0 відхиляється <br>
                {:else}
                    F<sub>A</sub> &gt; F<sub>крит</sub> - HO приймається <br>
                {/if}
                {#if Fb < Fcritical2}
                    F<sub>B</sub> &lt; F<sub>крит</sub> - H0 відхиляється <br>
                {:else}
                    F<sub>B</sub> &gt; F<sub>крит</sub> - HO приймається <br>
                {/if}
                {#if Fab >= Fcritical2}
                    F<sub>AB</sub> &GreaterEqual; F<sub>крит</sub> - H0 спростовується <br>
                {:else}
                    F<sub>AB</sub> &leq; F<sub>крит</sub> - HO не спростовується <br>
                {/if}
            </td>
        </tr>
        </tbody>
    </table>
</div>
<div class="jumbotron">
    <h4 class="display-3">Висновок:</h4>
    {#if Fab >= Fcritical2}
        <p class="lead">
            Вплив факторів А та В окремо кожного не є істотним, бо їх
            емпіричні значення Фішера менші, ніж критичні, тому H<sub>0</sub> приймається.
            Їх взаємодія, тобто вплив фактора А на обсяг відтворення слів при різних
            градаціях фактора В, є не однаковим, бо F<sub>емпАВ</sub> &GreaterEqual; F<sub>крит</sub> . Короткі слова
            краще
            запам’ятовуються, якщо їх диктують швидко, а довгі, коли їх дискутують
            повільно.</p>
        <p class="lead">При цьому можливо знайти силу впливу цього фактору</p>
        <p class="lead">n<sup>2</sup><sub>AB</sub> = {((SSab / SSall) * 100).toFixed(2)}%</p>
    {:else}
        F<sub>AB</sub> &leq; F<sub>крит</sub> - HO не спростовується <br>
    {/if}

</div>
