<script>

export default {

	props:['value', 'items'],
	methods:{

		itemTitle(it) {

			if ( it instanceof Object ) {
				return it.name || it.desc || it.id;
			} return it;

		},

		itemId(it){
			if ( it instanceof Object ) {
				return it.id;
			}
			return it;
		},

		setActive( it ) {

			this.$emit( 'input', it );
			this.$emit( 'changed', it );

		}

	}

}

</script>

<template>
	<div class="menu">

		<div class="menu-items">

		<div class="menu-item" v-for="(it) in items" :key="it.id">

			<span v-if="it != value" @click="setActive(it)" :key="itemTitle(it)"> <u> {{ itemTitle(it) }} </u></span>
			<span v-else :key="itemTitle(it)"> {{ itemTitle(it) }} </span>

		</div>

		</div>


		<!-- NOTE: slot css-class ignored -->
		<span class="menu-content"><slot :name="itemId(value)"></slot></span>

	</div>
</template>

<style scoped>
    .menu-items .menu-item { color: #000; }
    body.darkmode .menu-items .menu-item { color: #FFF; }
    .menu-items .menu-item u { color: #999; }
	.menu-items .menu-item:hover, .menu-items .menu-item u:hover { color: #33F; }

</style>