<template>
	<div class="frame">
		<!-- header -->
		<h1>{{ settings.title }}</h1>
		<hr>
		<h2>Dependencies</h2>
		<ul>
			<li v-for="dependency in settings.dependencies" :key="dependency">
				{{ dependency}}
			</li>
		</ul>
		<hr>
		
		<!-- content -->
		<slot></slot>
	</div>
</template>

<script>
export default {
	props:['settings']
}
</script>