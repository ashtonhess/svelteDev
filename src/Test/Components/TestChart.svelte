<script>
    import * as Pancake from '@sveltejs/pancake'; 
import data from '../../Historical/Components/data';

    let x1 = +Infinity;
    let x2 = -Infinity;
    let y1 = +Infinity;
    let y2 = -Infinity;

    let allData = [];

    let socket = new WebSocket('wss://stream.binance.com:9443/ws/btcusdt@trade');
    // let socket = new WebSocket('wss://stream.binance.com:9443/ws/btcusdt@ticker');

    //let subscribemsg = JSON.parse('{"method": "SUBSCRIBE","params": ["btcusdt@trade"],"id": 1}');

    socket.onmessage = function (event) {

        var data = JSON.parse(event.data);
        let dataDict = {'x':data['E'], 'y':parseFloat(data['p'])}
        // let dataDict = {'x':data['E'], 'y':parseFloat(data['c'])}
        if (dataDict.x>x2) x2 = dataDict.x;
        if (dataDict.x<x1) x1 = dataDict.x;
        if (dataDict.y>y2) y2 = dataDict.y;
        if (dataDict.y<y1) y1 = dataDict.y;
        if (x2-x1 > 60000) x1 =x2-60000
        
        allData.push(dataDict);
        allData=[...allData.filter(data => data.x > x1)]
        console.log(allData[allData.length-1]);
    }
    let closest;
    /* PAYLOAD STRUCTURE
    {
        "e": "trade",     // Event type
        "E": 123456789,   // Event time
        "s": "BNBBTC",    // Symbol
        "t": 12345,       // Trade ID
        "p": "0.001",     // Price
        "q": "100",       // Quantity
        "b": 88,          // Buyer order ID
        "a": 50,          // Seller order ID
        "T": 123456785,   // Trade time
        "m": true,        // Is the buyer the market maker?
        "M": true         // Ignore
    }
    */

</script>

<h3>Test Chart.</h3>
{#if allData.length>0}
<div class = "chart">
    <Pancake.Chart {x1} {x2} y1={y1} y2={y2}>
        <Pancake.Grid horizontal count={5} let:value>
            <div class="grid-line horizontal"><span>{value}</span></div>
        </Pancake.Grid>
        <Pancake.Grid vertical count={5} let:value>
            <span class ='x-label'>{String(new Date(value).toLocaleTimeString())}</span>
        </Pancake.Grid>
        <Pancake.Svg>
            <Pancake.SvgLine data={allData} let:d>
                <path class = 'data' {d}></path>
            </Pancake.SvgLine>
        </Pancake.Svg>

        {#if closest}
			<Pancake.Point x={closest.x} y={closest.y}>
				<span class="annotation-point"></span>
				<div class="annotation" style="transform: translate(-{100 * ((closest.x - x1) / (x2 - x1))}%,0)">
					<strong>{closest.y}</strong>
					<span>{closest.x}: {closest.y} years</span>
				</div>
			</Pancake.Point>
		{/if}

		<Pancake.Quadtree data={allData} bind:closest/>

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

	path.data {
		stroke: rgba(0,0,0,0.2);
		stroke-linejoin: round;
		stroke-linecap: round;
		stroke-width: 1px;
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

	.annotation span {
		display: block;
		font-size: 14px;
	}
</style>n    