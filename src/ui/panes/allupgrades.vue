<script>
import Game from 'game';
import {alphasort} from 'util/util';

export default {

	methods:{

		count(it){
			return it.value > 1 ? ( ' (' + Math.floor(it.value) + ')' ) : '';
		}
	},
	computed:{
		classes() {
			return Game.state.classes.filter(v=>!v.disabled&&v.value>=1);
		},
		tasks() {
			return Game.state.tasks.filter(v=>!v.repeat&&!v.disabled&&v.value>=1).sort(alphasort);
		},
		upgrades(){
			return Game.state.upgrades.filter(v=>!v.disabled&&v.value>=1).sort(alphasort);
		}
	}

}
</script>


<template>
<div class="allupgrades">
	<div class="div-hr">upgrades</div>
	<div class="up-list">
	<div v-for="it in classes" :key="it.id" @mouseenter.capture.stop="itemOver( $event,it)">{{it.name + count(it) }}</div>
	<div v-for="it in tasks" :key="it.id" @mouseenter.capture.stop="itemOver( $event,it)">{{it.name + count(it) }}</div>
	<div v-for="it in upgrades" :key="it.id" @mouseenter.capture.stop="itemOver( $event,it)">{{it.name + count(it) }}</div>
	</div>
</div>
</template>

<style scoped>

div.allupgrades {
	display:flex;
	flex-flow: column nowrap;
	height:100%;
}
div.up-list {
	margin-bottom:1rem;
	overflow-x:visible;
}

</style>