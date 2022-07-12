<script>
	import Historical from "./Historical/Historical.svelte"
	import Live from "./Live/Live.svelte"
	import Test from "./Test/TestMain.svelte"

	let sectionSelected = "Test";

</script>

<!--<main>-->
<!--	placeholder-->
<!--</main>-->

<div class="topnav">
  <a class="active" href="#home">Home</a>
  <a href="#news">Placeholder 1</a>
  <a href="#contact">Placeholder 2</a>
  <a href="#about">Placeholder 3</a>
</div>

<select class="sectionselector" bind:value={sectionSelected} >
	<option value={"Live"}>Live Trigger Counts</option>
	<option value={"Historical"}>Historical Trigger Counts</option>
	<option value={"Test"}>Testing</option>
</select>


{#if sectionSelected == "Live"}
	<Live/>
{:else if sectionSelected == "Historical"}	
	<Historical/>
{:else if sectionSelected == "Test"}	
	<Test/>
{/if}


<style>
	/* Add a black background color to the top navigation */
	.topnav {
	  background-color: #333;
	  overflow: hidden;
	}
	/* Style the links inside the navigation bar */
	.topnav a {
	  float: left;
	  color: #f2f2f2;
	  text-align: center;
	  padding: 14px 16px;
	  text-decoration: none;
	  font-size: 17px;
	}
	/* Change the color of links on hover */
	.topnav a:hover {
	  background-color: #ddd;
	  color: black;
	}
	/* Add a color to the active/current link */
	.topnav a.active {
	  background-color: #04AA6D;
	  color: white;
	}

	.sectionselector{
		padding-left: 1em;
	}
	
</style>




