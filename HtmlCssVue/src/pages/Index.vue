<template>
	<div>
		<div class="indev-view" v-show="show">
			<calendar-comp :show="show" :select-date="selectDate" v-on:calendar-event="calendarEvent">
			</calendar-comp>
		</div>
		<div class="indev-view" v-show="!show">
			<section class="train-index">
				<ul class="train-selform">
					<li class="train-station">
						<dd class="from" v-text="fromStation" :class="{exchange:isExchangeStation}"></dd>
						<dd class="to" v-text="toStation" :class="{exchange:isExchangeStation}"></dd>
						<dt @click="exchangeStation()" :style="{transform:'rotate(' + exchangeTimes * 360 +'deg)',
	                                webkitTransform:'rotate(' + exchangeTimes * 360 +'deg)'}">
	                    <i class="icon-change"></i>
	                  </dt>
					</li>
					<li class="train-time" @click="chooseDepartDate()">
						<dd>
							<em v-text="departMonthDay"></em>
							<span v-text="dayInfo"></span>
						</dd>
					</li>
					<li class="train-type">
						<div class="train-type-item" @click="chooseStudentTicket = !chooseStudentTicket">
							<span>学生票</span>
							<i :class="['train-type-checkbox', (isChooseStudentTicket ? 'train-type-checkbox-selected' : 'train-type-checkbox-unselected')]"></i>
						</div>
						<div class="train-type-item" @click="chooseHighTrain = !chooseHighTrain;">
							<span>高铁动车</span>
							<i :class="['train-type-checkbox', (isChooseHighTrain ? 'train-type-checkbox-selected' : 'train-type-checkbox-unselected')]"></i>
						</div>
					</li>
				</ul>
				<div class="train-index-btnbox">
					<button class="g_btn_s">查询</button>
				</div>
			</section>
			<ul class="train-list">
				<li>
					<i class="icon-eurail"></i> 国际火车票
				</li>
				<li @click="jsonpTest()">
					<i class="icon-eurail"></i> JSONP请求测试
				</li>
			</ul>
			<ul class="train-index-bnav">
				<li>
					<i class="icon-qiangpiao"></i>
					<em class="tag-chunyun">暑假</em> 抢票
				</li>
				<li>
					<i class="icon-seat"></i> 在线选座
				</li>
				<li>
					<i class="icon-status"></i> 身份免核验
				</li>
				<li @click="jumpPop">
					<i class="icon-order"></i> 我的订单
				</li>
			</ul>
		</div>
	</div>
</template>

<script>
	import mixin from "../lib/mixin.js";
	import Calendar from "../components/Calendar.vue";
	import {
		cUtil
	} from "../common/cUtil";
	export default {
		data() {
			return {
				isExchangeStation: false,
				exchangeTimes: 0,
				fromStation: "北京",
				toStation: "上海",
				show: false,
				chooseHighTrain: false,
				chooseStudentTicket: false
			};
		},
		mixins: [mixin],
		components: {
			"calendar-comp": Calendar
		},
		mounted() {},
		computed: {
			isChooseHighTrain() {
				return this.chooseHighTrain;
			},
			isChooseStudentTicket() {
				return this.chooseStudentTicket;
			},
			dayInfo() {
				if (this.selectDate) {
					return cUtil.getDayInfo(this.selectDate);
				}
			},
			departMonthDay() {
				if (this.selectDate) {
					return cUtil.getMonthDayStr(this.selectDate);
				}
			},
			selectDate() {
				if (this.$root.trainQuery) {
					return cUtil.parseDateStr(this.$root.trainQuery.date);
				}
			}
		},
		methods: {
			initPage(from) {
				var self = this;
				if (from && (from.path == '/')) {
					self.showLoading();
					setTimeout(function() {
						self.hiddenLoading();
					}, 2000);
					console.log("from" + JSON.stringify(from));
					//自定义返回事件
				}
				self.setHeader({
					title: '携程火车票',
					back: {
						tagname: 'back',
						callback: function() {
							if (self.show) {
								self.show = false;
							}
						}
					}
				}, self);
			},
			exchangeStation() {
				this.isExchangeStation = !this.isExchangeStation;
				this.exchangeTimes += 1;
				[this.toStation, this.fromStation] = [this.fromStation, this.toStation];
				let rect;
				if (this.isExchangeStation) {
					rect = document
						.getElementsByClassName("from")[0]
						.getBoundingClientRect();
				} else {
					rect = document.getElementsByClassName("to")[0].getBoundingClientRect();
				}
				//alert(JSON.stringify(this.$root.loadingOptions));
				console.log(this.fromStation, this.toStation, JSON.stringify(rect));
			},
			chooseDepartDate() {
				this.show = true;
			},
			calendarEvent(date) {
				this.show = false;
			},
			jumpPop() {
				router.push('/pop');
			},
			jsonpTest(){
				router.push('/jsonp');
			}
		},
		watch: {
			show: function(val) {
				this.setHeaderTitle(val ? "选择出发日期" : "国内火车票");
			}
		},
		created() {},
		beforeRouteEnter(to, from, next) {
			next(vm => {
				vm.initPage(from);
			});
		},
	};
</script>

<style scoped lang="less">

</style>