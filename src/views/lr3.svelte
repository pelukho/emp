<script>
    let defaultGroup = 10,
        fCritical = 2.99,
        courses = [2 * defaultGroup, defaultGroup, 3 * defaultGroup, 4, 2, 3, 1, 4, 5],
        privateSchool = [defaultGroup, defaultGroup, 2 * defaultGroup, 1, 2, 3, 1, 2, 3],
        bachelor = [2 * defaultGroup, defaultGroup, 2 * defaultGroup, 1, 2, 2, 3, 1, 3],
        magistr = [2 * defaultGroup, defaultGroup, 3 * defaultGroup, 2, 1, 3, 4, 2, 2],
        candidatOfScience = [3 * defaultGroup, 2 * defaultGroup, 4 * defaultGroup, 3, 4, 3, 3, 3, 3],
        summery = [],
        average = [],
        factors = [
            {
                name: 'курси',
                values: courses
            },
            {
                name: 'ліцей',
                values: privateSchool
            },
            {
                name: 'бакалавр',
                values: bachelor
            },
            {
                name: 'магістр',
                values: magistr
            },
            {
                name: 'кандидат наук',
                values: candidatOfScience
            }
        ],
        n = factors.length, j = courses.length, N = n * j;

    for (let i = 0, len = courses.length; i < len; i++) {
        summery.push(courses[i] + privateSchool[i] + bachelor[i] + magistr[i] + candidatOfScience[i]);
        average.push(summery[i] / n);
    }

    // D фактичне
    let dActual = arr => {
        let sum = arr.reduce((sum, item) => sum += item);
        let sumSquare = 0;

        arr.forEach(item => sumSquare += (item ** 2));

        return (((1 / n) * sumSquare) - ((1 / N) * (sum ** 2))).toFixed(2);
    };

    // D загальне
    let dAll = (arr1, arr2) => {
        let sum = arr1.reduce((sum, item) => sum += item);
        let sumSquare = 0;

        arr2.forEach(item => {
            item.values.forEach(item => sumSquare += (item ** 2));
        });

        return (sumSquare - (1 / N * (sum ** 2))).toFixed(2);
    };

    // D загальне
    let dInteral = (arr1, arr2, arr3) => {
        let sumSquare = 0;

        for (let i = 0, len = arr1.length; i < len; i++) {
            for (let j = 0, len = arr2.length; j < len; j++) {
                sumSquare += (arr2[j].values[i] - arr3[i]) ** 2;
            }
        }

        return (sumSquare).toFixed(2);
    };

    // MS факт
    let MsActual = (arr) => {
        return (dActual(arr) / (j - 1)).toFixed(2);
    };

    // MS залишкове
    let MsAll = (arr1, arr2, arr3) => {
        return (dInteral(arr1, arr2, arr3) / ((N - 1) - (j - 1))).toFixed(2);
    };

    let kActual = j - 1,
        kAll = N - 1,
        kSub = kAll - kActual;
</script>

<div class="table-responsive mb-5">
    <table class="table">
        <thead>
        <tr>
            <th>Фактор</th>
            <th colspan="{courses.length}" class="text-center">Заробітна плата, тисяч умовних одиниць</th>
        </tr>
        </thead>
        <tbody>
        {#each factors as factor, i}
            <tr>
                <td>{factor.name}</td>
                {#each factor.values as val}
                    <td>{val}</td>
                {/each}
            </tr>
        {/each}
        <tr>
            <td>&sum; - {summery.reduce((s, item) => s += item)}</td>
            {#each summery as sum}
                <td>{sum}</td>
            {/each}
        </tr>
        <tr>
            <td>X середне</td>
            {#each average as ave}
                <td>{ave.toFixed(2)}</td>
            {/each}
        </tr>
        </tbody>
    </table>
</div>

<h2 class="text-center">Розв’язання</h2>
<div class="jumbotron">
    <h3 class="display-3">Дано</h3>
    <hr class="my-4">
    <p class="lead">
        n = {n} - кількість суб’єктів (об’єктів) у кожній групі;
    </p>
    <p class="lead">
        j = {j} - кількість градацій;
    </p>
    <p class="lead">
        N = {N} - загальне число індивідуальних значень;
    </p>
    <hr class="my-4">

    <h4>1. Обчислюємо</h4>
    <p>D<sub>факт</sub> = S<sub>факт</sub> = {dActual(summery)}</p>
    <hr class="my-4">

    <h4>2. Обчислюємо</h4>
    <p>D<sub>заг</sub> = S<sub>заг</sub> = {dAll(summery, factors)}</p>
    <hr class="my-4">

    <h4>3. Обчислюємо</h4>
    <p>D<sub>внутр-групова ( залишкова )</sub> = S<sub>внутр-групова ( залишкова )</sub>
        = {dInteral(courses, factors, average)}</p>
    <hr class="my-4">

    <h4>Перевірка:</h4>
    <p>SS<sub>зал</sub> = SS<sub>заг</sub> - SS <sub>факт</sub> = {dAll(summery, factors)} - {dActual(summery)}
        = {(dAll(summery, factors) - dActual(summery)).toFixed(2)}</p>
    <hr class="my-4">

    <h4>4. Визначимо число степенів вільності</h4>
    <p>k<sub>факт</sub> = j - 1 = {j} - 1 = {kActual};</p>
    <p>k<sub>заг</sub> = N - 1 = {N} - 1 = {kAll};</p>
    <p>k<sub>залишк</sub> = k<sub>заг</sub> - k<sub>факт</sub> = {N - 1} - {j - 1} = {kSub}</p>
    <hr class="my-4">

    <h4>5. Обчислюємо</h4>
    <p>MS<sub>факт</sub> = {MsActual(summery)}</p>
    <p>MS<sub>залишк</sub> = {MsAll(courses, factors, average)}</p>
    <hr class="my-4">

    <h4>6. Обчислюємо</h4>
    <p>F<sub>emp</sub> = MS<sub>факт</sub> / MS<sub>залишк</sub> = {MsActual(summery)}
        / {MsAll(courses, factors, average)}
        = {(MsActual(summery) / MsAll(courses, factors, average)).toFixed(2)}</p>
    <hr class="my-4">

    <h4>Знаходимо в таблиці критичні значення критерія Фішера для &#11393; = 0,01</h4>
    <p>F<sub>крит</sub>( &#11393; , k<sub>факт</sub> , k<sub>залиш</sub>) = F<sub>крит</sub>( 0,01;{kActual};
        {kSub}) =
        <label class="col-form-label col-form-label-lg" style="display: inline; width: auto;">
            <input class="form-control form-control-lg" style="display: inline; width: auto;" type=number
                   bind:value={fCritical}>
        </label>
    </p>
    <hr class="my-4">

    <h4>8. Висновок.</h4>
    {#if fCritical !== 0 && (MsActual(summery) / MsAll(courses, factors, average)).toFixed(2) >= fCritical}
        <p>Оскільки F<sub>емп</sub> &#8805; F<sub>крит</sub>, то H<sub>0</sub> - відхиляється,</p>
        <p>Приймається H<sub>1</sub> - фактор освіти на заробітну плату програмістів вливає.</p>
        <p>При цьому можливо знайти силу впливу цього фактору</p>
        <p>n<sup>2</sup> = (SS<sub>факт</sub>/SS<sub>загальна</sub>) * 100% = ({dActual(summery)}
            / {dAll(summery, factors)}) * 100%
            = {Math.ceil((dActual(summery) / dAll(summery, factors)) * 100)}%</p>
    {:else if fCritical !== 0 && (MsActual(summery) / MsAll(courses, factors, average)).toFixed(2) <= fCritical}
        <p>Оскільки F<sub>емп</sub> &#8804; F<sub>крит</sub>, то H<sub>1</sub> - відхиляється,</p>
        <p>Приймається H<sub>0</sub> - фактор освіти на заробітну плату програмістів не вливає.</p>
    {/if}
</div>
