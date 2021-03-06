<script>
import Game from '../../game';
import { centerX, positionAt } from './popups.js';

import ItemsBase from '../itemsBase.js';
import GData from '../../items/gdata';

/**
 * @note The Choice Mechanism is highly convoluted right now, due to Vue interactions
 * and poor structure on my part. slotpick button emits an event to open the choice popup.
 * It might be better to place choice as a component of slotpick?
 * @emits choice
 */
export default {

	data() {
		return {
			title:null,
			list:null,
			elm:null,
			item:null,
			open:false,
			mustPay:false,
			strings:false
		}
	},
	mixins:[ItemsBase],
	created(){

		this.cb = null;

		this.plisten = ()=>{

			/**
			 * Special event to show choice dialog.
			*/
			this.add( 'choice', this.show, this );

		};

		this.listen('game-loaded', this.plisten );

	},
	beforeDestroy(){
		this.removeListener( 'game-loaded', this.plisten );
		this.plisten = null;
		this.cancel();
	},
	updated() {

		if (this.open===false) return;

		if ( this.elm) positionAt( this.$el, this.elm, 0 );
		else centerX( this.$el );

	},
	computed:{

		/**
		 * @property {gdata[]} choices - used to convert strings or tag string into choice objects.
		 */
		choices:{

			get(){ return this.list; },
			set(v){

				if ( typeof v === 'string') v = Game.state.getData( v );

				if ( v && v.items ) v = v.items;
				if ( Array.isArray(v ) ) {

					if ( this.strings ) this.list = v;
					else {
						var a = [];
						for( let i = v.length-1; i>= 0; i-- ) {

							var it = Game.state.findData( v[i] );
							if ( it ) a.push(it);
						}

						this.list = a;
					}

				} else this.list = null;

			}

		}

	},
	methods:{

		/**
		 * @param {object} opts - dispaly options
		 * @param {?HTMLElement} opts.elm - element to display relative to.
		 * @param {?function} opts.cb - callback
		 * @param {?string} opts.title - choice title.
		 * @param {?boolean} opts.mustPay - must pay to select.
		 * @param {?boolean} opts.strings - choices given as raw strings.
		 */
		show( choices, opts ) {

			//console.log('showing choices');
			this.title = opts.title;
			this.cb = opts.cb;
			this.elm = opts.elm;
			this.strings = opts.strings;
			this.mustPay = opts.mustPay;

			this.choices = choices;
			this.open=true;

		},

		cantPay(it) {
			return it.cost&&!Game.canPay(it.cost);
		},

		choose( opt ){

			this.open = false;
			this.item = null;
			this.choices = null;
			this.elm = null;

			if ( this.cb ) {

				let cb = this.cb;
				this.cb = null;
				cb( opt );

			}

		},
		cancel(){
			this.open=false;
			this.cb = null;
			this.item = null;
			this.choices = null;
			this.elm = null;
		}

	}

}
</script>

<template>
<div class="popup" v-if="choices!=null&&choices.length>0">
	<div class="content">

		<span class="title" v-if="title">{{title}}</span>

		<div class="items">

		<button class="task-btn"
			:disabled="!strings&&!slottable(it)||(mustPay&&cantPay(it))"
			v-for="it in choices" :key="strings?it:it.id"
			@mouseenter.capture.stop="!strings ? itemOver( $event,it):''"
			@click="choose( it )">{{ strings ? it : it.name }}</button>
		</div>

		<button class="close-btn" @click="cancel">Cancel</button>

	</div>
</div>

</template>

<style scoped>

.popup {
	z-index: var(--md-depth);
	max-width: 50vw;
}
.content {
	display:flex;
	flex-flow: column nowrap;
	width: 100%;
	min-height:5rem;
}

.content .items {
	display:flex;
	flex-flow: row wrap;
	flex-grow: 1;
	width:auto;
}

.content .title {
	font-weight: bold;
	text-align: center;
	margin-bottom: var(--sm-gap);
}

.task-btn {

	max-height: 2em;
	width:100%;
}

button.close-btn {
	min-height: 2em;
	width: 5em;
	font-size:0.9em;
}


</style>