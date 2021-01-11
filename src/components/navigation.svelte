<script>
    import Router, {link} from 'svelte-spa-router'
    import active from 'svelte-spa-router/active'
    import Home from '../views/home.svelte'
    import LR1 from '../views/lr1.svelte'
    import LR2 from '../views/lr2.svelte'
    import LR3 from '../views/lr3.svelte'
    import LR4 from '../views/lr4.svelte'
    import LR5 from '../views/lr5.svelte'
</script>

<ul class="nav nav-pills mb-5">
    <li class="nav-item">
        <a class="nav-link" href="/" use:link use:active={{className: 'active'}}>Головна</a>
    </li>
    <li class="nav-item">
        <a class="nav-link" use:link use:active={{className: 'active'}} href="/lr1">ЛР 1</a>
    </li>
    <li class="nav-item">
        <a class="nav-link" use:link use:active={{className: 'active'}} href="/lr2">ЛР 2</a>
    </li>
    <li class="nav-item">
        <a class="nav-link" use:link use:active={{className: 'active'}} href="/lr3">ЛР 3</a>
    </li>
    <li class="nav-item">
        <a class="nav-link" use:link use:active={{className: 'active'}} href="/lr4">ЛР 4</a>
    </li>
    <li class="nav-item">
        <a class="nav-link" use:link use:active={{className: 'active'}} href="/lr5">ЛР 5</a>
    </li>
</ul>

<Router routes={{
	'/': Home,
	'/lr1': LR1,
	'/lr2': LR2,
	'/lr3': LR3,
	'/lr4': LR4,
	'/lr5': LR5
}}/>