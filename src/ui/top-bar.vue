<script>
import Profile from 'modules/profile';
import Game from '../game';

import LoginStatus from './components/loginStatus.vue';

export default {

	computed:{
		hasHall(){return Profile.hasHall() },
		VERSION(){return __VERSION; },
		DEV(){return !__DIST},

	},
	created(){
		this.Profile = Profile;
	},
	components:{
		login:LoginStatus
	},
	methods:{

		fileSelect(e) {

			e.stopPropagation();
			e.preventDefault();

			this.dispatch( 'load-file', e.target.files );

		},

		fileDrop(e){
			e.stopPropagation();
			e.preventDefault();

			e.target.classList.remove('hilite');

			const dt = e.dataTransfer;
			this.dispatch('load-file', dt.files);

		},
		fileDrag(e){
			e.stopPropagation();
			e.preventDefault();
			e.currentTarget.classList.add('hilite');
		},
		dragOut(e){
			e.stopPropagation();
			e.preventDefault();
			e.currentTarget.classList.remove('hilite');
		},

	}

}
</script>

<template>

	<div class="top-bar">

		<span class="load-opts">
		<login v-if="Profile.CLOUD" />
		<button @click="dispatch('save')">save</button>
		<button @click="dispatch('load')">load</button>

		<div class="text-button"><a href=""
			@click.self="dispatch('save-file',$event )" type="text/json">get save</a></div>

		<div v-if="hasHall" class="text-button"><a href=""
			@click.self="dispatch('hall-file',$event )" type="text/json">hall save</a></div>

			<!--<input type="file" name="[File]" accept="text/json" @change="fileDrop">-->
		<button id="drop-file" @click="$refs.fileInput.click()" @drop="fileDrop" @dragover="fileDrag" @dragleave.capture.stop="dragOut" name="[Load Save]">[Load Save]</button>
			<input ref="fileInput" type="file" @change="fileSelect" accept="text/json text/*">

			<confirm @confirm="dispatch('reset')">reset wizard</confirm>

		<span class="devWarn" v-if="DEV">UNSTABLE BUILD</span>

		</span>

		<span class="items"><slot name="center"></slot></span>

		<span class="link-bar">
			<a href="https://discord.gg/bCABC96" target="_blank">discord</a>
			<a href="http://wiki.lerpinglemur.com/dhrune" target="_blank">wiki</a>
			<a href="https://lerpinglemur.gitlab.io/arcanum/" target="_blank">unstable</a>
			<!--<a href="https://www.patreon.com/theoryofmagic" target="_blank">patreon</a>-->
			<a href="https://www.reddit.com/r/wizrobe/" target="_blank">reddit</a>
			<span class="vers">vers {{ VERSION }}</span>
			<button class="text-button" @click="$emit('open-settings')">&#9881;</button>
		</span>

	</div>


</template>

<style scoped>

span.vers {
	font-size: 0.9em;
	align-self: center;
	margin-bottom: var(--sm-gap);
}

div.text-button a {
	text-decoration: none;
}

input[type="file"] {
	display:none;
}

#drop-file {
	border: var(--tiny-gap) dashed var(--quiet-text-color);
}

.load-opts {
	display:flex;
}

.load-opts button {
	height:100%;
}
.load-opts .text-button {
	max-height:var(--lg-gap);
}

span.load-message {
	padding: var(--md-gap) var(--md-gap) var(--tiny-gap);
}

</style>