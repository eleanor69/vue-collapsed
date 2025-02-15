<script setup lang="ts">
import { ref, computed } from 'vue';
import ExampleHeader from './ExampleHeader.vue';
import fakeData from './fakeData.json';

const questions = ref(
	fakeData.map(({ title, answer }, index) => ({
		title,
		answer,
		isExpanded: index === 1 || index === 5, // Initial values, display expanded on mount
	}))
);

function handleIndividual(selectedIndex: number) {
	questions.value[selectedIndex].isExpanded = !questions.value[selectedIndex].isExpanded;
}

/**
 * Accessibility attributes
 *
 * https://www.w3.org/WAI/ARIA/apg/example-index/accordion/accordion
 */

const toggleAttrs = computed(() =>
	questions.value.map((question, index) => ({
		id: `toggle_${index}`,
		'aria-expanded': question.isExpanded,
		'aria-controls': `collapse_${index}`,
	}))
);

const collapseAttrs = computed(() =>
	questions.value.map((_, index) => ({
		'aria-labelledby': `toggle_${index}`,
		id: `collapse_${index}`,
		role: 'region',
	}))
);
</script>

<template>
	<section class="Wrapper">
		<ExampleHeader
			title="Individual Control"
			href="blob/main/app/IndividualControl.vue"
			hint="With aria attributes — Using ref()"
		/>
		<div v-for="(question, index) in questions" :key="question.title" class="Section">
			<button
				v-bind="toggleAttrs[index]"
				@click="() => handleIndividual(index)"
				:class="[
					'Panel',
					{
						Active: question.isExpanded,
					},
				]"
			>
				{{ question.title }}
			</button>
			<Collapse :when="question.isExpanded" class="Collapse" v-bind="collapseAttrs[index]">
				<p>
					{{ question.answer }}
				</p>
			</Collapse>
		</div>
	</section>
</template>

<!-- Check styles in App.vue -->
