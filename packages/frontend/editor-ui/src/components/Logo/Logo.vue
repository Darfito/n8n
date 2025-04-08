<script setup lang="ts">
import type { FrontendSettings } from '@n8n/api-types';
import { computed, onMounted, useCssModule } from 'vue';
import { useFavicon } from '@vueuse/core';

import KalbeLogo from './Kalbe_Farma-full-putih.png';

const props = defineProps<
	(
		| {
				location: 'authView';
		  }
		| {
				location: 'sidebar';
				collapsed: boolean;
		  }
	) & {
		releaseChannel: FrontendSettings['releaseChannel'];
	}
>();

const { location, releaseChannel } = props;

/**
 * Hitung kumpulan kelas CSS berdasarkan lokasi dan status `collapsed`.
 */
const $style = useCssModule();
const containerClasses = computed(() => {
	if (location === 'authView') {
		return [$style.logoContainer, $style.authView];
	}
	return [
		$style.logoContainer,
		$style.sidebar,
		'collapsed' in props && props.collapsed ? $style.sidebarCollapsed : $style.sidebarExpanded,
	];
});

/**
 * Opsional: jika bukan channel `stable`, ubah favicon menjadi PNG kita.
 */
onMounted(() => {
	if (releaseChannel !== 'stable') {
		useFavicon(KalbeLogo);
	}
});
</script>

<template>
	<div :class="containerClasses" data-test-id="n8n-logo">
		<!-- Hanya menampilkan 1 file PNG -->
		<img :src="KalbeLogo" :class="$style.logo" alt="Kalbe Logo" />
		<slot />
	</div>
</template>

<style lang="scss" module>
.logoContainer {
	display: flex;
	justify-content: center;
	align-items: center;
}

/**
 * Logo lebih besar saat di authView
 */
.authView {
	transform: scale(2);
	margin-bottom: var(--spacing-xl);
}

/**
 * Logo di sidebar (baik collapsed maupun expanded)
 */
.logo {
	transform: scale(1.3) translateY(-2px);
}

.sidebarExpanded .logo {
	margin-left: var(--spacing-2xs);
}

.sidebarCollapsed .logo {
	width: 40px;
	height: 30px;
	padding: 0 var(--spacing-4xs);
}
</style>
