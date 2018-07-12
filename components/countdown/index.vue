<template>
	<transition name="countdown">
		<div v-if='showCountdown' class="zmiti-countdown-main-ui" @touchend='touchend' @touchstart='getStartArea($event)' @touchmove="modifyScore">
			<span></span>
			<div class="zmiti-countdown-sure" v-tap='[getScore]'>确定</div>
			<section class="zmiti-countdown-box">
				<div class="zmiti-countdown-main" @transitionend='end1' :class="{'active':isTransition}" :style="{WebkitTransform:'rotateX('+rotate+'deg)'}">
					<div>
						<span>0</span>
						<div>
							<span>1</span>
							<div>
								<span>2</span>
								<div>
									<span>3</span>
									<div>
										<span>4</span>
										<div>
											<span>5</span>
											<div>
												<span>6</span>
												<div>
													<span>7</span>
													<div>
														<span>8</span>
														<div>
															<span>9</span>
														</div>
													</div>
												</div>
											</div>
										</div>
									</div>
								</div>
							</div>
							
						</div>
					</div>
				</div>
			</section>
			<section class="zmiti-countdown-box">
				<div class="zmiti-countdown-main" @transitionend='end1' :class="{'active':isTransition}" :style="{WebkitTransform:'rotateX('+rotate1+'deg)'}">
					<div>
						<span>0</span>
						<div>
							<span>1</span>
							<div>
								<span>2</span>
								<div>
									<span>3</span>
									<div>
										<span>4</span>
										<div>
											<span>5</span>
											<div>
												<span>6</span>
												<div>
													<span>7</span>
													<div>
														<span>8</span>
														<div>
															<span>9</span>
														</div>
													</div>
												</div>
											</div>
										</div>
									</div>
								</div>
							</div>
							
						</div>
					</div>
				</div>
			</section>
		</div>
	</transition>
</template>

<script>
	import './index.css';
	import zmitiUtil from '../lib/util';
	export default {
		props:['obserable','team1','team2'],
		name:'zmitiindex',
		data(){
			return{
				rotate:180,
				rotate1:180,
				type:'rotate',
				score1:0,
				score2:0,
				scoreType:'score1',
				viewW:window.innerWidth,
				transY:0,
				isTransition:true,
				showCountdown:false,
				end:false
			
			}
		},
		components:{
		},
		
		methods:{
			
			end1(){
				if(this.end){
					this.isTransition = false;
					this[this.type]%=360;
					
					if(this.swipe === 'down'){
						this[this.scoreType] = -(this[this.type]-180) / 36 % 10;
					}else{
						this[this.scoreType] =(10 -(this[this.type]-180) / 36 % 10)%10;
					}
					//console.log(this[this.scoreType] ,this[this.type]-180,(this[this.type]-180) / 36);
					this.end = false;
				}
			},

			getStartArea(e){
				var e = e.changedTouches[0];
				this.type = e.pageX<this.viewW/2 ? 'rotate':'rotate1';
				this.scoreType = e.pageX<this.viewW/2 ? 'score1':'score2';
				this.transY = e.pageY;
				this.isTransition = true;

				


				
			},

			modifyScore(e){
				this.end = false;
				var oneAng = 360 / 10;
				var e = e.changedTouches[0];
				this[this.type] +=(this.transY - e.pageY)/ 12;
			},
			touchend(e){
				this.end = true;
				this.isTransition = true;
				var oneAng = 360 / 10;
				var e = e.changedTouches[0];
				var defaultRotate = this.defaultRotate;
				if(this.type  === 'rotate1'){
					defaultRotate = this.defaultRotate1;
				}
				var scale = ((this[this.type]-defaultRotate) / oneAng|0);
				this[this.type] = defaultRotate + scale * oneAng;
				
				if(this.transY > e.pageY){
					this.swipe = 'up'
				}else{
					this.swipe = 'down'
					
				}

				//console.log(this[this.scoreType],this[this.type],(this[this.type]%360 - 180 )/oneAng);
				this.transY = e.pageY
				
				

				//this[this.type] = scale * oneAng +180;
				///this[this.type] %= 360;
				//this[this.type] += this[this.type] % oneAng;
				
			},
			getScore(){
				var {obserable} = this;
				console.log(obserable);
				var s = this;
				obserable.trigger({
					type:'fillScore',
					data:{
						score1:s.score1,
						score2:s.score2
					}
				});

				obserable.trigger({
					type:'toggleCountdownPage',
					data:{
						show:false
					}
				});

				obserable.trigger({
					type:"showResultPage"
				})
			}
		

			
		},
		mounted(){
			window.s = this;
			this.rotate -= this.team1.score * 360/10;
			this.defaultRotate = this.rotate;
			this.rotate1 -= this.team2.score * 360/10;
			this.defaultRotate1 = this.rotate1;
			this.score1 = this.team1.score;
			this.score2 = this.team2.score;
			var {obserable} = this;
			obserable.on('toggleCountdownPage',(data)=>{
				this.showCountdown = data.show
			})
			


		}
	}
</script>