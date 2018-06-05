<template>
	<div class="comments">
		<div v-if="comments.length > 1" class="comment__item" v-for="comment in reduceComments" :key="comment.id">
				<div class="comment__item-image">
					<img :src="comment.picture" class="comment__item-image">
				</div>
				<div class="comment__item-content">
					<span class="comment__item-content__name">{{ comment.name }}</span>
					<span class="comment__item-content__body">{{ comment.message }}</span>
				</div>
		</div>
		<template v-if="comments.length === 0">
			<div class="loading">
				<span>Chưa có bình luận nào</span>
			</div>
		</template>
	</div>
</template>

<script>
	import axios from 'axios'
	import { intervalForCommentsComponent, ID_POST, ACCESS_TOKEN } from '../config'

	export default {
		data() {
			return {
				comments: [],
				endpoint: 'https://graph.facebook.com/v3.0',
				next: ''
			}
		},
		computed: {
			reduceComments() {
				return this.comments.reduce((acc, cur) => {
					acc.push({
						id: cur.id, 
						name: cur.from.name,  
						picture: cur.from.picture.data.url,
						message: cur.message
					})
					return acc
				}, [])
			}
		},
		methods: {
			getCommentFromApi() {
				setInterval(() => {
					axios.get(`${this.endpoint}/${ID_POST}/comments?access_token=${ACCESS_TOKEN}&pretty=0&fields=from%7Bname%2Cpicture%7D%2Cmessage&limit=1&after=${this.next}`)
						.then(res => {
							if(res.data.data.length > 0) {
								this.next = res.data.paging.cursors.after
								this.comments = [...res.data.data, ...this.comments]
							}
							return 
						})
				}, intervalForCommentsComponent)				
			}
		},
		created() {
			this.getCommentFromApi()
		}
	}
</script>

<style>
.comments {
	grid-column: 6 / -1;
	grid-row: 1 / -1;
	border: 3px solid var(--white-color);
  margin: 1.5rem 1.5rem 1.5rem .5rem;
  border-radius: 7px;
  background: var(--black-color);
  overflow: hidden;
}

.comment__item {
	display: flex;
  align-items: flex-start;
  padding: 6px;
}

.comment__item-image {
	flex: 0 0 50px;
  height: 50px;
  margin-top: 2px;
}

.comment__item-image img {
	border-radius: 4px;
  width: 100%;
  height: 100%;
}

.comment__item-content {
	display: flex;
  flex-direction: column;
  color: #fff;
  margin-left: 6px;
}

.comment__item-content__name {
	font-size: 1.1rem;
  font-weight: 800;
  letter-spacing: .025rem;
}

.comment__item-content__body {
	font-size: .95rem;
  color: var(--white-color);
  letter-spacing: .03rem;
  overflow-wrap: break-word;
	word-wrap: break-word;
}

.loading {
	display: flex;
  justify-content: center;
  align-items: center;
  color: #ccc;
  width: 100%;
  height: 100%;
  font-size: 1.5rem;
  letter-spacing: .025rem;
}
</style>