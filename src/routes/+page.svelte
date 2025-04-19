<script lang="ts">
	import Carousel from '$lib/components/Carousel.svelte';
	import type { ISlide } from '$lib/components/CarouselTypes';
	const carouselItems: ISlide[] = [
		{
			slideId: 1,
			image: 'https://placehold.co/800x300?text=Slide+1',
			alt: 'Slide 1',
			caption: 'First Slide'
		},
		{
			slideId: 2,
			image: 'https://placehold.co/800x300?text=Slide+2',
			alt: 'Slide 2',
			caption: 'Second Slide'
		},
		{
			slideId: 3,
			image: 'https://placehold.co/800x300?text=Slide+3',
			alt: 'Slide 3',
			caption: 'Third Slide'
		}
	];
</script>

<Carousel slides={carouselItems} />
