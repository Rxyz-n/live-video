<template>
<div class="playlist">
		<div class="song__info">
			<span>{{ rename }}</span>
		</div>
		<div class="song__progress-bar">
			<div class="progress" ref="progress"></div>
		</div>
		<div class="song__controlls">
			<button class="song__controlls--prev buttons">
				<i class="fas fa-step-backward"></i>
			</button>
			<button class="song__controlls--play buttons">
				<i class="far fa-play-circle"></i>
			</button>
			<button class="song__controlls--next buttons">
				<i class="fas fa-step-forward"></i>
			</button>
		</div>
		<canvas id="analyser-render"></canvas>
	</div>
</template>

<script>
	import axios from 'axios'
	
	export default {
		data() {
			return {
				path: 'assets/musics',
				audio: new Audio(),
				playlist: [],
				currentIndex: 0,
				currentNameSong: '',
				analyser: '',
				ctx: '',
				canvas: ''
			}
		},
		methods: {
			frameLooper(){
				window.requestAnimationFrame(this.frameLooper)
				const array = new Uint8Array(this.analyser.frequencyBinCount)
				this.analyser.getByteFrequencyData(array)
				this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height)
				this.ctx.fillStyle = '#00CCFF'
				const bars = 100
				for (var i = 0; i < bars; i++) {
					const x = i * 3
					const width = 2
					const height = -(array[i] / 2)
					this.ctx.fillRect(x, this.canvas.height, width, height)
				}
			},
			async initPlayer() {
				const songsResponse = await axios.get(this.path)
				this.playlist = songsResponse.data
				this.currentNameSong = this.playlist[this.currentIndex]
				this.audio.src = `${this.path}/${this.playlist[this.currentIndex]}`
				this.audio.play()

				const context = new AudioContext(); 
				this.analyser = context.createAnalyser();
				this.canvas = document.getElementById('analyser-render');
				this.ctx = this.canvas.getContext('2d');
				
				const source = context.createMediaElementSource(this.audio); 
				source.connect(this.analyser);
				this.analyser.connect(context.destination);
				this.frameLooper();
			},
			timeupdate() {
				let nt = this.audio.currentTime * (100 / this.audio.duration)
				this.$refs.progress.style.width = nt + '%'
			},
			switchSong() {
				if(this.playlist.length - 1 === this.currentIndex) {
					this.currentIndex = 0
				} else {
					this.currentIndex++
				}
				this.currentNameSong = this.playlist[this.currentIndex]
				this.audio.src = `${this.path}/${this.playlist[this.currentIndex]}`
				this.$refs.progress.style.width = 0
				this.audio.play()
			}
		},
		computed: {
			rename() {
				return this.currentNameSong.replace('.mp3', '')
			}
		},
		async created() {
			await this.initPlayer()
			this.audio.addEventListener('timeupdate', this.timeupdate)
			this.audio.addEventListener('ended', this.switchSong)
		}
	}
</script>

<style>
	.playlist {
		grid-column: 1 / 6;
    grid-row: 6 / -1;
    border: 3px solid var(--white-color);
    margin: 1.5rem;
    border-radius: 7px;
    background: rgba(0, 0, 0, .7);
    display: flex;
    flex-direction: column;
    color: #fff;
    justify-content: center;
    align-items: center;
		position: relative;
	}

	.song__info {
		font-size: 1.7rem;
    letter-spacing: .025rem;
    margin: 0 auto 1rem auto;
    width: 55%;
    text-align: center;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
	}

	.song__progress-bar {
		background: #333;
    height: 8px;
    width: 55%;
    border-radius: 7px;
    margin-bottom: 1.5rem;
	}

	.song__progress-bar .progress {
		width: 0;
    height: 8px;
    background: #fff;
    border-radius: 7px;
	}

	.buttons {
		background: transparent;
    outline: none;
    border: none;
    color: #fff;
    font-size: 1.5rem;
    margin-right: 10px;
	}

	.song__controlls--play {
		font-size: 2rem;
	}

	#analyser-render{
		width: 100%;
    height: 100%;
    position: absolute;
    z-index: -9;
	}
</style>