<script>

    import * as Pancake from '@sveltejs/pancake'

    export let header = '';

    export let x1=+Infinity;
    export let x2=-Infinity;
    export let y1=+Infinity;
    export let y2=-Infinity;

    export let data = [];

    let closest;

</script>

<div class='chart'>
    <h3 class='header'>{header}</h3>
    <Pancake.Chart {x1} {x2} {y1} {y2}>
        <Pancake.Grid horizontal count = {4} let:value>
            <div class='grid-line horizontal'><span>${value.toLocaleString()}</span></div>
        </Pancake.Grid>
        <Pancake.Grid vertical count = {4} let:value>
            <span class = 'x-label'>{String(new Date(value).toLocaleTimeString())}</span>
        </Pancake.Grid>
        <Pancake.Svg>
            <Pancake.SvgLine data={data} let:d>
                <path class='data' {d}></path>
            </Pancake.SvgLine>
        </Pancake.Svg>

        {#if closest}
        <Pancake.Point x={closest.x} y={closest.y}>
            <span class="annotation-point"></span>
            <div class="annotation" style="transform: translate(-{100 * ((closest.x - x1) / (x2 - x1))}%,0)">
                <strong>${closest.y.toLocaleString()}</strong>
                <!-- <span>{closest.x}: {closest.y}</span> -->
                <span>{String(new Date(closest.x).toLocaleTimeString())}</span>
            </div>
        </Pancake.Point>
        {/if}

        <Pancake.Quadtree data={data} bind:closest/>

    </Pancake.Chart>
</div>


<style>
    .header{
        left: 0em;
        /* left: -3em; */
        /* position: relative; */
        /* display: block; */
    }

    .chart{
        height: 250px;
        width: 90%;
        padding: 2em 2em 2em 2em;
        margin: 0 0 36px 0;
        padding-left: 1em;
        /* padding-right: 4em; */
    }
    .grid-line{
        position: relative;
        display: block;
    }
    .grid-line.horizontal{
        width: calc(100% + 4em);
        /* left: -4em; */
        left: -4em;
        border-bottom: 1px dashed #ccc;
    }
    .grid-line span{
        position: absolute;
        left: 0em;
        bottom: 2px;
        font-family: sans-serif;
        font-size: 14px;
        color: #999;
    }
    .x-label{
        position: absolute;
        width: 4em;
        left: -2em;
        bottom: -3em;
        font-family: sans-serif;
        font-size: 14px;
        color: #999;
        text-align: center;
    }
    path.data{
        stroke: rgba(0, 0, 0, 0.2);
        stroke-linejoin: round;
        stroke-linecap: round;
        stroke-width: 2px;
        fill: none;
    }




    .annotation {
		position: absolute;
		white-space: nowrap;
		bottom: 1em;
		line-height: 1.2;
		background-color: rgba(255,255,255,0.9);
		padding: 0.2em 0.4em;
		border-radius: 2px;
	}
	.annotation-point {
		position: absolute;
		width: 10px;
		height: 10px;
		background-color: #ff3e00;
		border-radius: 50%;
		transform: translate(-50%,-50%);
	}
	.annotation strong {
		display: block;
		font-size: 20px;
	}
	.annotation span {
		display: block;
		font-size: 14px;
	}
</style>
