<script lang="ts">
	import { onDestroy } from 'svelte';
	import type { ISlide } from './CarouselTypes';

	export let slides: ISlide[] = [];
	export let autoplay = true;
	export let interval = 10000;
	export let primaryColor = '36, 50, 84';
	export let indicatorSizeInRem = '1';

	let currentSlideId = 1;
	let timer: number;
	let sliderPaused = false;
	let currentSlide: ISlide | undefined = slides.find((s) => s.slideId === currentSlideId);

	let previousSlide = () => {
		const previousSlideId = currentSlideId - 1;
		if (previousSlideId <= 0) {
			currentSlideId = 1;
		} else {
			currentSlideId--;
		}
		currentSlide = slides.find((s) => s.slideId === currentSlideId);
	};
	let nextSlide = () => {
		const nextSlideId = currentSlideId + 1;
		if (nextSlideId > slides.length) {
			currentSlideId = 1;
		} else {
			currentSlideId++;
		}
		currentSlide = slides.find((s) => s.slideId === currentSlideId);
	};
	let goToSlide = (slideNumber: number) => {
		resetSlider();
		currentSlideId = slideNumber;
		currentSlide = slides.find((s) => s.slideId === currentSlideId);
	};

	$: if (autoplay) {
		resetSlider();
	}

	let resetSlider = () => {
		clearInterval(timer);
		timer = setInterval(() => {
			if (!sliderPaused) {
				nextSlide();
			}
		}, interval);
	};
	let pauseSlider = () => (sliderPaused = true);
	let resumeSlider = () => (sliderPaused = false);

	onDestroy(() => clearInterval(timer));
</script>

<div
	class="container"
	style="--primary: {primaryColor}; --indicator-size: {indicatorSizeInRem}rem;"
>
	{#if currentSlide}
		<h2 class="caption">{currentSlide.caption}</h2>
	{/if}
	<div
		class="carousel"
		role="article"
		on:focus={pauseSlider}
		on:mouseover={pauseSlider}
		on:mouseleave={resumeSlider}
	>
		{#each slides as slide (slide.image)}
			<div class="carousel-item" class:active={slide.slideId === currentSlideId}>
				<div class="slide">
					<img src={slide.image} alt={slide.alt} />
				</div>
			</div>
		{/each}
	</div>

	{#if slides.length > 1}
		<button class="control-btn previous" on:click={previousSlide}>❮</button>
		<button class="control-btn next" on:click={nextSlide}>❯</button>

		<div class="indicators">
			{#each slides as _, index}
				<button
					type="button"
					aria-label="Go to slide {index}"
					class="indicator"
					class:active={index + 1 === currentSlideId}
					on:click={() => goToSlide(index + 1)}
				></button>
			{/each}
		</div>
	{/if}
</div>

<style>
	.container {
		display: grid;
		place-items: center;
		place-content: center;
		margin: -8px;
	}
	.container > * {
		grid-area: container;
		max-width: 1500px;
	}

	.carousel-item {
		display: none;
		transition: transform 500ms cubic-bezier(0.25, 1, 0.5, 1);
	}

	.carousel-item.active {
		display: flex;
	}

	.indicators {
		display: flex;
		gap: 1rem;
		padding: 1rem;
		place-self: end center;
	}

	.indicator {
		padding: 0;
		font-size: 0;
		color: transparent;
		border: 3px solid white;
		background-color: white;
		border-radius: 50%;
		width: var(--indicator-size);
		height: var(--indicator-size);
		cursor: pointer;
	}

	.indicator.active {
		background-color: rgba(var(--primary), 1);
	}

	.slide > img {
		aspect-ratio: 16 / 9;
		object-fit: cover;
		width: 100%;
		height: 100%;
		user-select: none;
	}

	.caption {
		margin-left: 10px;
		place-self: end start;
		z-index: 1;
		color: rgba(var(--primary));
	}

	.control-btn {
		font-size: 2.5em;
		background: transparent;
		border: none;
		cursor: pointer;
		color: rgba(var(--primary), 1);
	}

	.control-btn:hover {
		color: rgba(var(--primary), 0.5);
	}

	.control-btn.previous {
		place-self: center start;
	}

	.control-btn.next {
		place-self: center end;
	}
</style>
