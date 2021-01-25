<script>
  export let segment;
  
  $:() => {
    console.log(segment)
  }

</script>

<h1>嵌套路由</h1>

<div class="submenu">
	<a href="about">about</a>
	<a href="about/cort">cort</a>
	<a href="about/good">good</a>
</div>

<slot></slot>



