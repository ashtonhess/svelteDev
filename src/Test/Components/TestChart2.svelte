<script>
    import * as Pancake from '@sveltejs/pancake'; 
    // import data from '../../Historical/Components/data';

    let x1 = +Infinity;
    let x2 = -Infinity;
    let y1 = +Infinity;
    let y2 = -Infinity;

    // let x1n = +Infinity;
    // let x2n = -Infinity;
    // let y1n = +Infinity;
    // let y2n = -Infinity;

    let posData = [{'2022-01-01':10},{'2022-01-02':15},{'2022-01-03':35}];
    let negData = [{'2022-01-01':20},{'2022-01-02':33},{'2022-01-03':40}];
    let pData = [];
    let nData = [];
    let dates = [];
    // $: posData.forEach(thing => console.log(thing.values()))
    // let keys = Object.keys(posData);
    // console.log(keys)
    
    
    // var values = Object.keys(posData).map(function(key){
    //     //console.log(posData[key])
    //     return posData[key];
    // });
    // console.log(values)
    $: counter = 0;
    for (let x = 0; x < posData.length; x++) {
        //const element = array[index];
        //console.log(index)
        for(let key in posData[x]) {
            dates.push(key);
            let value = posData[x][key];
            let dataDict = {'x': x, 'y': value};
            pData.push(dataDict);
            if (dataDict.x>x2) x2 = dataDict.x;
            if (dataDict.x<x1) x1 = dataDict.x;
            if (dataDict.y>y2) y2 = dataDict.y;
            if (dataDict.y<y1) y1 = dataDict.y;
            //counter++;
            //console.log(counter)
            // do something with "key" and "value" variables
        }
    }
    for (let x = 0; x < negData.length; x++) {
        for(let key in negData[x]) {
            dates.push(key);
            let value = negData[x][key];
            let dataDict = {'x': x, 'y': value};
            nData.push(dataDict);
            if (dataDict.x>x2) x2 = dataDict.x;
            if (dataDict.x<x1) x1 = dataDict.x;
            if (dataDict.y>y2) y2 = dataDict.y;
            if (dataDict.y<y1) y1 = dataDict.y;
        }
    }
    $: console.log(dates)
    $: console.log(pData);
    $: console.log(nData)

    //$: allData = posData;
    
    // $: posData.forEach((val, idx) => console.log(val, idx))
    // $: console.log([...posData.map(date => date.value])
    // $: posData.forEach((val, idx) => allData.push(({x: val, y:idx})))
    // $: for (const [key, value] of Object.entries(posData)) {
    //     console.log(key, value);
    // }

    // $: posData.forEach( i => console.log(i.get()))


    // function addToData()

    //let socket = new WebSocket('wss://stream.binance.com:9443/ws/btcusdt@trade');
    // socket.onmessage = function (event) {
    //     var data = JSON.parse(event.data);
    //     let dataDict = {'x':data['E'], 'y':parseFloat(data['p'])}
    //     // let dataDict = {'x':data['E'], 'y':parseFloat(data['c'])}
    //     if (dataDict.x>x2) x2 = dataDict.x;
    //     if (dataDict.x<x1) x1 = dataDict.x;
    //     if (dataDict.y>y2) y2 = dataDict.y;
    //     if (dataDict.y<y1) y1 = dataDict.y;
    //     if (x2-x1 > 60000) x1 =x2-60000        
    //     allData.push(dataDict);
    //     allData=[...allData.filter(data => data.x > x1)]
    //     console.log(allData[allData.length-1]);
    // }
    let closest;

</script>

<h3>Test Chart.</h3>
{#if (pData.length>0)&&(nData.length>0)}

<div class = "chart">
    <Pancake.Chart {x1} {x2} y1={y1} y2={y2}>
        <Pancake.Grid horizontal count={5} let:value>
            <div class="grid-line horizontal"><span>{value}</span></div>
        </Pancake.Grid>
        <Pancake.Grid vertical count={dates.length} let:value>
            <!-- <span class ='x-label'>{String(new Date(value).toLocaleTimeString())}</span> -->
            {#if dates[value]}
            <span class ='x-label'>{dates[value]}</span>
            {/if}
        </Pancake.Grid>
        <Pancake.Svg>
            <Pancake.SvgLine data={pData} let:d>
                <path class = 'pdata' {d}></path>
            </Pancake.SvgLine>
            <Pancake.SvgLine data={nData} let:d>
                <path class = 'ndata' {d}></path>
            </Pancake.SvgLine>
        </Pancake.Svg>

        {#if closest}
			<Pancake.Point x={closest.x} y={closest.y}>
				<span class="annotation-point"></span>
				<div class="annotation" style="transform: translate(-{100 * ((closest.x - x1) / (x2 - x1))}%,0)">
					<strong>{closest.y}</strong>
					<!-- <span>{closest.x}: {closest.y} years</span> -->
				</div>
			</Pancake.Point>
		{/if}
        <Pancake.Quadtree data={nData} bind:closest/>
		<Pancake.Quadtree data={pData} bind:closest/>

    </Pancake.Chart>
</div>
{/if}

<style>
    .chart {
		height: 400px;
		padding: 3em 0 2em 2em;
		margin: 0 0 36px 0;
	}

	/* input {
		font-size: inherit;
		font-family: inherit;
		padding: 0.5em;
	} */

	.grid-line {
		position: relative;
		display: block;
	}

	.grid-line.horizontal {
		width: calc(100% + 2em);
		left: -2em;
		border-bottom: 1px dashed #ccc;
	}

	.grid-line span {
		position: absolute;
		left: 0;
		bottom: 2px;
		font-family: sans-serif;
		font-size: 14px;
		color: #999;
	}

	.x-label {
		position: absolute;
		width: 4em;
		left: -2em;
		bottom: -22px;
		font-family: sans-serif;
		font-size: 14px;
		color: #999;
		text-align: center;
	}

	path.pdata {
		stroke: rgba(203, 24, 24, 0.8);
		stroke-linejoin: round;
		stroke-linecap: round;
		stroke-width: 4px;
		fill: none;
	}
    path.ndata {
		stroke: rgba(75, 203, 24, 0.8);
		stroke-linejoin: round;
		stroke-linecap: round;
		stroke-width: 4px;
		fill: none;
	}

	/* .highlight {
		stroke: #ff3e00;
		fill: none;
		stroke-width: 2;
	} */

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

	/* .annotation span {
		display: block;
		font-size: 14px;
	} */
</style>