<script>
    let defaultGroup = 20,
        fCritical = 3.83,
        dzal = 0,
        courses = [2 * defaultGroup, defaultGroup, 3 * defaultGroup, 4, 2, 3, 1, 4, 5],
        privateSchool = [defaultGroup, defaultGroup, 2 * defaultGroup, 1, 2, 3, 1, 2, 3],
        bachelor = [2 * defaultGroup, defaultGroup, 2 * defaultGroup, 1, 2, 2, 3, 1, 3],
        magistr = [2 * defaultGroup, defaultGroup, 3 * defaultGroup, 2, 1, 3, 4, 2, 2],
        candidatOfScience = [3 * defaultGroup, 2 * defaultGroup, 4 * defaultGroup, 3, 4, 3, 3, 3, 3],
        s2 = [],
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
        summery = factors.map(item => {
            return item.values.reduce((s,i) => {return s+=i}, 0)
        }),
        n = courses.length, j = factors.length, N = n * j,
        average = summery.map(item => item / n);

    for (let i = 0, len = courses.length; i < len; i++) {
        s2.push((courses[i] ** 2) + (privateSchool[i] ** 2) + (bachelor[i] ** 2) + (magistr[i] ** 2) + (candidatOfScience[i] ** 2))
    }

    s2 = s2.reduce((sum, item) => {
        return sum += item
    }, 0);

    let kfac = j - 1,
        kzag = N - 1,
        kzal = kzag - kfac;

    for (let i = 0, len = courses.length; i < len; i ++) {
        for(let j = 0, len = factors.length; j < len; j++) {
            dzal += ((factors[j].values[i] - average[j]) ** 2);
        }
    }

    let ev2 = summery.reduce((sum, item) => {
            return sum += (item ** 2)
        }, 0),
        esum = summery.reduce((sum, item) => {
            return sum += item
        }, 0),
        ex2 = (1 / N) * (esum ** 2),
        dfac = (((1 / n) * ev2) - (ex2)),
        dzag = (s2 - (ex2)),
        MSfac = dfac / kfac,
        MSzal = dzal / kzal,
        femp = MSfac / MSzal;

</script>

<div class="table-responsive mb-5">
    <table class="table">
        <thead>
        <tr>
            <th>Фактор</th>
            <th>{factors[0].name}</th>
            <th>{factors[1].name}</th>
            <th>{factors[2].name}</th>
            <th>{factors[3].name}</th>
            <th>{factors[4].name}</th>
        </tr>
        </thead>
        <tbody>
        {#each factors[0].values as f, i}
            <tr>
                <td>{i + 1}</td>
                <td>{factors[0].values[i]}</td>
                <td>{factors[1].values[i]}</td>
                <td>{factors[2].values[i]}</td>
                <td>{factors[3].values[i]}</td>
                <td>{factors[4].values[i]}</td>
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
    <p>D<sub>факт</sub> = S<sub>факт</sub> = {dfac.toFixed(2)}</p>
    <hr class="my-4">

    <h4>2. Обчислюємо</h4>
    <p>D<sub>заг</sub> = S<sub>заг</sub> = {dzag.toFixed(2)}</p>
    <hr class="my-4">

    <h4>3. Обчислюємо</h4>
    <p>D<sub>внутр-групова ( залишкова )</sub> = S<sub>внутр-групова ( залишкова )</sub>
        = {dzal.toFixed(2)}</p>
    <hr class="my-4">

    <h4>Перевірка:</h4>
    <p>SS<sub>зал</sub> = SS<sub>заг</sub> - SS <sub>факт</sub> = {dzag.toFixed(2)} - {dfac.toFixed(2)}
        =  {dzag.toFixed(2) - dfac.toFixed(2)}</p>
    <hr class="my-4">

    <h4>4. Визначимо число степенів вільності</h4>
    <p>k<sub>факт</sub> = j - 1 = {j} - 1 = {kfac};</p>
    <p>k<sub>заг</sub> = N - 1 = {N} - 1 = {kzag};</p>
    <p>k<sub>залишк</sub> = k<sub>заг</sub> - k<sub>факт</sub> = {N - 1} - {j - 1} = {kzal}</p>
    <hr class="my-4">

    <h4>5. Обчислюємо</h4>
    <p>MS<sub>факт</sub> = {MSfac.toFixed(2)}</p>
    <p>MS<sub>залишк</sub> = {MSzal.toFixed(2)}</p>
    <hr class="my-4">

    <h4>6. Обчислюємо</h4>
    <p>F<sub>emp</sub> = MS<sub>факт</sub> / MS<sub>залишк</sub> = {MSfac.toFixed(2)}
        / {MSzal.toFixed(2)}
        = {femp.toFixed(2)}</p>
    <hr class="my-4">

    <h4>Знаходимо в таблиці критичні значення критерія Фішера для &#11393; = 0,01</h4>
    <p>F<sub>крит</sub>( &#11393; , k<sub>факт</sub> , k<sub>залиш</sub>) = F<sub>крит</sub>( 0,01;{kfac};
        {kzal}) =
        <label class="col-form-label col-form-label-lg" style="display: inline; width: auto;">
            <input class="form-control form-control-lg" style="display: inline; width: auto;" type=number
                   bind:value={fCritical}>
        </label>
    </p>
    <hr class="my-4">

    <h4>8. Висновок.</h4>
    {#if fCritical !== 0 && femp.toFixed(2) >= fCritical}

        <p>Оскільки F<sub>емп</sub> &#8805; F<sub>крит</sub>, то H<sub>0</sub> - відхиляється,</p>
        <p>Приймається H<sub>1</sub> - фактор освіти на заробітну плату програмістів вливає.</p>
        <p>При цьому можливо знайти силу впливу цього фактору</p>
        <p>
            n<sup>2</sup> = (SS<sub>факт</sub>/SS<sub>загальна</sub>) * 100% = ({1}
            / {1}) * 100%
            = {Math.ceil((dfac / dzag) * 100)}%
        </p>

    {:else if fCritical !== 0 && (femp).toFixed(2) <= fCritical}
        <p>Оскільки F<sub>емп</sub> &#8804; F<sub>крит</sub>, то H<sub>1</sub> - відхиляється,</p>
        <p>Приймається H<sub>0</sub> - фактор освіти на заробітну плату програмістів не вливає.</p>
    {/if}
</div>
