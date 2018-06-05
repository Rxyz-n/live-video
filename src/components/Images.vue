<template>
	<div class="images">
		<transition name="fade" mode="out-in">
			<img :src="currentImage" :key="currentIndex" />
		</transition>
	</div>
</template>

<script>
	import axios from 'axios'
	import { intervalForImagesComponent } from '../config'

	export default {
		data() {
			return {
				path: 'assets/images',
				images: [],
				currentImage: '',
				currentIndex: 0,

			}
		},
		methods: {
			async getImages() {
				const imagesResponse = await axios.get(this.path)
				this.images = imagesResponse.data
				this.currentImage = `${this.path}/${this.images[this.currentIndex]}`
			},
			changeImage() {
				setInterval(() => {
					this.currentIndex++
					if(this.images.length === this.currentIndex) {
						this.currentIndex = 0
					}
					this.currentImage = `${this.path}/${this.images[this.currentIndex]}`
				}, intervalForImagesComponent)
			}
		},
		async created() {
			await this.getImages()
			this.changeImage()
		}
	}
</script>

<style>
.images {
	grid-column: 1 / 6;
  grid-row: 1 / 6;
  border: 3px solid var(--white-color);
  margin: 1.5rem;
  border-radius: 7px;
  background: #000;
}

.images img {
	width: 100%;
	height: 100%;
	border-radius: 4px;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s linear;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>