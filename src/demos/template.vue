<template>
	<div class="frame">
		<!-- header -->
		<h2>{{ settings.title }}</h2>
		<hr>
		<h3>Dependencies</h3>
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