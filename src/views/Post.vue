<template>
	<div class="Post">
		<div class="grid-item" @click="$router.push(`${base_url}/images`)">
			<img alt="" :src="cover_photo_url" />
			<span class="images-button">Images</span>
		</div>

		<PostBlock
			@click="$router.push(`${base_url}/map`)"
			:text="humanReadableLocation"
			button
		>
			<IconLocation />
		</PostBlock>

		<PostBlock :text="distance + 'km traveled'" />
		<PostBlock text="" />

		<PostBlock
			@click="$router.push(`${base_url}/blog`)"
			:text="'Day #' + day"
			button
		>
			<IconBlog />
		</PostBlock>
	</div>
</template>

<script>
import IconLocation from '@/components/IconLocation'
import IconBlog from '@/components/IconBlog'

import PostBlock from '@/components/PostBlock'

export default {
	components: { IconBlog, IconLocation, PostBlock },
	data: () => ({
		post: null,
		location: null,
	}),
	computed: {
		base_url() { return `/posts/${this.post.id}` },
		distance() { return this.post ? this.post.distance : '' },
		day() { return this.post ? this.post.date.getDate() : '' },
		cover_photo_url() { return this.post ? this.post.images[0].url : ''},
		humanReadableLocation() {
			if (!this.location) return ''

			const location = []

			if (this.location.town) location.push(this.location.town)
			location.push(this.location.country)

			return location.join(', ')
		}
	},
	async created() {
		this.post = await this.$store.dispatch('posts/getPost', { id: this.$route.params.id })

		this.location = await this.$store.dispatch('location/getLocation', {
			coords: this.post.coords
		})
	}
}
</script>

<style lang="scss" scoped>
@import 'src/assets/palette';

.Post {
	display: grid;
	grid-template-columns: 50% 50%;

	.grid-item {
		height: 171px;
		border: 3px solid $primary-color;

		position: relative;

		line-height: 2em;

		display: flex;
		justify-content: center;
		align-items: center;

		span {
			position: absolute;

			color: $primary-color;
			font-weight: bold;

			padding: 3em 6em;

			background-color: rgba(black, .3);
		}
	}

	.grid-item:first-child {
		grid-row: 1 / span 2;
		grid-column: 1 / span 2;

		cursor: pointer;

		height: 100%;
	}
}

</style>
