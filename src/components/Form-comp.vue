<script setup>
// eslint-disable-next-line no-unused-vars
const props = defineProps(['submit', 'inputChange', 'buttonClick', 'cities']);
import { vMaska } from 'maska';
</script>

<template>
	<div class="row">
		<div class="col-md-12">
			<form @submit.prevent="submit">
				<div class="row px-3">
					<div class="col-xl-2 col-md-5 col-sm-5 col-10">
						<input
							@keyup="inputChange"
							type="text"
							v-maska
							data-maska="A A"
							data-maska-tokens="A:[a-zA-Z]:multiple"
							id="city-input"
							spellcheck="false"
							autocomplete="off"
							class="bg-trasparent form-control"
						/>
						<div class="invalid-feedback position-absolute">Forneça uma cidade válida.</div>
					</div>
					<div class="col-2 btn-col">
						<button @click="buttonClick" type="submit" class="btn btn-primary btn-search">
							<i class="bi bi-search"></i>
						</button>
					</div>
				</div>
				<div class="autocomplete">
					<div v-show="cities[0]" class="row autocomplete-row ms-3 mt-2" :class="{ last: !cities[1] }">
						<button class="col-md-12 autocomplete-col pe-1 text-start city" :value="cities[0]">
							{{ cities[0] }}
						</button>
					</div>
					<div v-show="cities[1]" class="row autocomplete-row ms-3" :class="{ last: !cities[2] }">
						<button class="col-md-12 autocomplete-col pe-1 text-start city" :value="cities[1]">
							{{ cities[1] }}
						</button>
					</div>
					<div v-show="cities[2]" class="row autocomplete-row ms-3" :class="{ last: cities[2] }">
						<button class="col-md-12 autocomplete-col pe-1 text-start city" :value="cities[2]">
							{{ cities[2] }}
						</button>
					</div>
				</div>
			</form>
		</div>
	</div>
</template>

<style scoped lang="scss">
#city-input {
	@media (orientation: portrait) and (min-width: 768px) {
		font-size: 24px;
	}

	@media (orientation: portrait) and (max-width: 767px) {
		font-size: 18px;
	}
}

.btn {
	&-col {
		display: flex;
	}

	&-search {
		@media (orientation: portrait) {
			width: 50px;
		}
	}
}

.last {
	.autocomplete-col {
		border-bottom-left-radius: 4px;
		border-bottom-right-radius: 4px;
		border: none;
	}
}

.selected {
	background-color: rgb(235, 235, 235) !important;
}

.autocomplete {
	height: 120px;
	position: absolute;
	font-family: Arial, Helvetica, sans-serif;
	font-size: 14px;
	z-index: 1;

	@media (max-width: 991px) {
		font-size: 20px;
	}

	@media (max-width: 767px) {
		font-size: 16px;
	}

	&,
	&-row {
		width: 217px;
		height: 40px;
	}

	&-row:first-child {
		.autocomplete-col {
			border-top-left-radius: 4px;
			border-top-right-radius: 4px;
		}
	}

	&-col {
		box-shadow:
			rgba(17, 17, 26, 0.05) 2px 2px 0px,
			rgba(17, 17, 26, 0.1) 0px 2px 2px;
		border: none;
		background-color: #f0f0f0;
		max-height: 40px;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
		border-bottom: 2px solid #d6d6d6;

		&:hover {
			background-color: #ebebeb;
		}
	}
}
</style>
