<script>
	let w;
	let h;
	let size = 42;
	let text = 'измени меня';
</script>

<style>
	input { display: block; }
	div { display: inline-block; }
	span { word-break: break-all; }
</style>

<input type=range bind:value={size}>
<input bind:value={text}>

<p>размер: {w}px x {h}px</p>

<div>
	<span style="font-size: {size}px">{text}</span>
</div>