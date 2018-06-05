<template>
  <div id="vue">
		<Images />
		<Comments />
		<Playlist />
  </div>
</template>

<script>
	import Images from './components/Images.vue'
	import Comments from './components/Comments.vue'
	import Playlist from './components/Playlist.vue'

	export default {
		components: {
			Images,
			Comments,
			Playlist
		}
	}
</script>

<style>
	@import url('https://fonts.googleapis.com/css?family=Nunito:400,800&subset=latin-ext,vietnamese');

	:root {
		--black-color: rgba(0, 0, 0, .9);
		--white-color: rgba(255, 255, 255, .9);
	}

	body {
		margin: 0;
		padding: 0;
		width: 100%;
		height: 100vh;
		overflow: hidden;
		background: url('/assets/bg.jpg');
		background-size: cover;
		background-position: center;
		background-repeat: no-repeat;
		box-sizing: border-box;
		font-family: 'Nunito', sans-serif;
	}

	#vue {
		width: 100%;
		height: 100%;
		display: grid;
		grid-template-columns: repeat(7, 1fr);
		grid-template-rows: repeat(8, 1fr);
	}
</style>
