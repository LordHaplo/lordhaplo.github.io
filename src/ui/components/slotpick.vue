<script>
import Game from '../../game';

import ItemsBase from '../itemsBase.js';
import { SET_SLOT } from '../../events';

/**
 * Component to display a button to pick current slot item from all available slot items.
 * Extremely convoluted; clicking the button then displays a choice dialog through events.
 * @todo replace this entire component.
 */
export default {

	/**
	 * @property {string} pick - slot to pick item for. (slot is Vue reserved)
	 * @property {?string} title - name to display to user. defaults to pick.
	 * @property {?Item[]} choices - list of items to filter. if not set, all game items are tested
	 * for a matching slot property.
	 * @property {?string} pickEvent - event to emit on item picked.
	 */
	props:['pick', 'title', 'choices', 'pickEvent', 'hideEmpty', 'mustPay'],
	mixins:[ItemsBase],
	data(){
		return {
			pEvent:this.pickEvent||SET_SLOT,
			cur:Game.state.getSlot(this.pick)
		};
	},
	methods:{

		toggleChange(){ this.changing = !this.changing; },

		select(){

			// @todo messy to emit. hard to config with Vue.
			this.emit( 'choice', this.avail, {
				cb:(p)=>{

					if ( p ) {
						this.emit( this.pEvent, p );
						this.cur = Game.state.getSlot(this.pick);
					}

				},
				elm:this.$el,
				title:this.title||this.pick,
				mustPay:this.mustPay
			});

		}

	},
	computed:{

		avail() {
			return this.choices ? this.choices :
			Game.state.filterItems( v=>v.slot===this.pick&&!v.disabled&&!v.locks&&!v.locked
								&&(v.owned||!v.buy) );
		}

	}

}
</script>

<template>
<div v-if="!hideEmpty||avail.length>0">

	<span v-if="title">{{title}}:</span>
	<button class="task-btn" @mouseenter.capture.stop="itemOver($event,cur)" @click="select" v-if="avail.length>0">{{ cur ? cur.name : 'None'}}</button>

</div>
</template>