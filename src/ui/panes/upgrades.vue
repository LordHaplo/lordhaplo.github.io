<script>
import ItemsBase from '../itemsBase.js';
//import UIMixin from './uiMixin';

export default {

	/**
	 * @property {string} event - name of event to fire when an item is selected.
	 */
	props:['items', 'event', 'target'],
	mixins:[ItemsBase],
	data(){
		return {
			pevent:this.event||'upgrade',
			targ:this.target||undefined
		}
	}

}
</script>


<template>
<div class="upgrades">

	<!--<div><button ref="btnHides" class="btnConfig">&#9881;</button></div>-->

	<button :class="{'task-btn':true, hidable:true, locked:locked(it)||(it.owned&&!it.repeat),
		running:it.running, runnable:it.perpetual||it.length>0,
		disabled:!it.canUse() }"
		v-for="it in items" :data-key="it.id" :key="it.id"
		@mouseenter.capture.stop="itemOver( $event,it)"

		@click="emit( pevent, it, targ )">{{ it.name }}</button>

</div>
</template>