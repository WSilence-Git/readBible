<template>
  <view>
    <fui-status-bar background="#fff"></fui-status-bar>
    <fui-calendar @change="dateSelect" :isMultiple="false" :value="today"></fui-calendar>

    <!--    阅读记录-->
    <view v-if="pointDate !== today">
      <view v-for="(group,index) in groups" :key="index">
        <fui-checkbox-group name="checkbox">
          <view class="fui-list__item">
            <fui-label v-for="(item,iindex) in readLog" :key="iindex">
              <view class="fui-align__center" v-if="item.group===group">
                <fui-checkbox :disabled="true" :checked="true" :value="item.value" borderRadius="8rpx"></fui-checkbox>
                <text class="fui-text">{{ `${item.name} ${item.chapter}` }}</text>
              </view>
            </fui-label>
          </view>
        </fui-checkbox-group>
      </view>
      <!--      <view style="text-align: center;margin-top: 20px">当日阅读{{ readLog.length }}章</view>-->
    </view>
    <!--    今日阅读计划-->
    <view v-if="pointDate === today">
      <view v-for="(group,index) in groups" :key="index" class="group">
        <fui-checkbox-group name="checkbox">
          <view class="fui-list__item" style="width: 300px;">
            <fui-label v-for="(item,iindex) in readPlan" :key="iindex" style="display: inline-flex;position: relative">
              <view class="fui-align__center" v-if="item.group===group">
                <fui-checkbox
                    :disabled="!!item.date" :checked="!!item.date" :value="item.value"
                    borderRadius="8rpx" color="#09be4f"
                    @change="readCheck($event,item.flag)">
                </fui-checkbox>
                <text class="fui-text">{{ `${item.name} ${item.chapter}` }}</text>
              </view>
              <view class="play-code" v-if="item.group===group">{{ item.code }}</view>
            </fui-label>
          </view>
        </fui-checkbox-group>
        <view class="tag-wrap">
          <fui-tag text="+1" size="26" @click="getReadPlan(group,1)" type="success"></fui-tag>
        </view>
      </view>
      <view style="text-align: center;margin-top: 20px">今日阅读{{ readPlan.filter(item => item.date).length }}章</view>
    </view>
  </view>
</template>

<script>
const db = uniCloud.database();

export default {
  name: 'Plan',
  data() {
    return {
      pointDate: '',
      books: [
        {
          name: '创', //创
          count: 50,
          group: 'A',
          sn: 1
        },
        {
          name: '出', //出
          count: 40,
          group: 'A',
          sn: 51
        },
        {
          name: '利',
          count: 27,
          group: 'A',
          sn: 91
        },
        {
          name: '民',
          count: 36,
          group: 'A',
          sn: 118
        },
        {
          name: '申',
          count: 34,
          group: 'A',
          sn: 154
        },
        {
          name: '书',
          count: 24,
          group: 'A',
          sn: 188
        },
        {
          name: '士',
          count: 21,
          group: 'A',
          sn: 212
        },
        {
          name: '得',
          count: 4,
          group: 'A',
          sn: 233
        },
        {
          name: '撒上',
          count: 31,
          group: 'A',
          sn: 237
        },
        {
          name: '撒下',
          count: 24,
          group: 'A',
          sn: 268
        },
        {
          name: '王上',
          count: 22,
          group: 'A',
          sn: 292
        },
        {
          name: '王下',
          count: 25,
          group: 'A',
          sn: 314
        },
        {
          name: '代上',
          count: 29,
          group: 'A',
          sn: 339
        },
        {
          name: '代下',
          count: 36,
          group: 'A',
          sn: 368
        },
        {
          name: '拉',
          count: 10,
          group: 'A',
          sn: 404
        },
        {
          name: '尼',
          count: 13,
          group: 'A',
          sn: 414
        },
        {
          name: '斯',
          count: 10,
          group: 'A',
          sn: 427
        },
        {
          name: '伯',
          count: 42,
          group: 'A',
          sn: 437
        },
        {
          name: '诗',
          count: 150,
          group: 'D',
          sn: 479
        },
        {
          name: '箴',
          count: 31,
          group: 'C',
          sn: 629
        },
        {
          name: '传',
          count: 12,
          group: 'A',
          sn: 660
        },
        {
          name: '歌',
          count: 8,
          group: 'A',
          sn: 672
        },
        {
          name: '赛',
          count: 66,
          group: 'A',
          sn: 680
        },
        {
          name: '耶',
          count: 52,
          group: 'A',
          sn: 746
        },
        {
          name: '哀',
          count: 5,
          group: 'A',
          sn: 798
        },
        {
          name: '结',
          count: 48,
          group: 'A',
          sn: 803
        },
        {
          name: '但',
          count: 12,
          group: 'A',
          sn: 851
        },
        {
          name: '何',
          count: 14,
          group: 'A',
          sn: 863
        },
        {
          name: '珥',
          count: 3,
          group: 'A',
          sn: 877
        },
        {
          name: '摩',
          count: 9,
          group: 'A',
          sn: 880
        },
        {
          name: '俄',
          count: 1,
          group: 'A',
          sn: 889
        },
        {
          name: '拿',
          count: 4,
          group: 'A',
          sn: 890
        },
        {
          name: '弥',
          count: 7,
          group: 'A',
          sn: 894
        },
        {
          name: '鸿',
          count: 3,
          group: 'A',
          sn: 901
        },
        {
          name: '哈',
          count: 3,
          group: 'A',
          sn: 904
        },
        {
          name: '番',
          count: 3,
          group: 'A',
          sn: 907
        },
        {
          name: '该',
          count: 2,
          group: 'A',
          sn: 910
        },
        {
          name: '亚',
          count: 14,
          group: 'A',
          sn: 912
        },
        {
          name: '玛',
          count: 4,
          group: 'A',
          sn: 926
        },
        {
          name: '太',
          count: 28,
          group: 'B',
          sn: 930
        },
        {
          name: '可',
          count: 16,
          group: 'B',
          sn: 958
        },
        {
          name: '路',
          count: 24,
          group: 'B',
          sn: 974
        },
        {
          name: '约',
          count: 21,
          group: 'B',
          sn: 998
        },
        {
          name: '徒',
          count: 28,
          group: 'B',
          sn: 1019
        },
        {
          name: '罗',
          count: 16,
          group: 'B',
          sn: 1047
        },
        {
          name: '林前',
          count: 16,
          group: 'B',
          sn: 1063
        },
        {
          name: '林后',
          count: 13,
          group: 'B',
          sn: 1079
        },
        {
          name: '加',
          count: 6,
          group: 'B',
          sn: 1092
        },
        {
          name: '弗',
          count: 6,
          group: 'B',
          sn: 1098
        },
        {
          name: '腓',
          count: 4,
          group: 'B',
          sn: 1104
        },
        {
          name: '西',
          count: 4,
          group: 'B',
          sn: 1108
        },
        {
          name: '帖前',
          count: 5,
          group: 'B',
          sn: 1112
        },
        {
          name: '帖后',
          count: 3,
          group: 'B',
          sn: 1117
        },
        {
          name: '提前',
          count: 6,
          group: 'B',
          sn: 1120
        },
        {
          name: '提后',
          count: 4,
          group: 'B',
          sn: 1126
        },
        {
          name: '多',
          count: 3,
          group: 'B',
          sn: 1130
        },
        {
          name: '门',
          count: 1,
          group: 'B',
          sn: 1133
        },
        {
          name: '来',
          count: 13,
          group: 'B',
          sn: 1134
        },
        {
          name: '雅',
          count: 5,
          group: 'B',
          sn: 1147
        },
        {
          name: '彼前',
          count: 5,
          group: 'B',
          sn: 1152
        },
        {
          name: '彼后',
          count: 3,
          group: 'B',
          sn: 1157
        },
        {
          name: '约壹',
          count: 5,
          group: 'B',
          sn: 1160
        },
        {
          name: '约贰',
          count: 1,
          group: 'B',
          sn: 1165
        },
        {
          name: '约叁',
          count: 1,
          group: 'B',
          sn: 1166
        },
        {
          name: '犹',
          count: 1,
          group: 'B',
          sn: 1167
        },
        {
          name: '启',
          count: 22,
          group: 'B',
          sn: 1168
        }
      ],
      readLog: [],
      readLogCache: {},
      readPlan: [],
      groups: ['A', 'B', 'C', 'D'],
    }
  },
  onLoad() {
    this.setReadPlan()
  },
  computed: {
    today() {
      const date = new Date();
      const year = date.getFullYear().toString().padStart(4, '0');
      const month = (date.getMonth() + 1).toString().padStart(2, '0');
      const day = date.getDate().toString().padStart(2, '0');
      return `${year}-${month}-${day}`
    }
  },
  methods: {
    // 初始化当天数据
    setReadPlan() {
      let data = {
        value: this.today
      }
      this.dateSelect(data)
    },
    // 执行配置
    async startPlan() {
      let readConfig = [
        {group: 'A', times: 3},
        {group: 'B', times: 2},
        {group: 'C', times: 1},
        {group: 'D', times: 4}
      ]
      for (let i = 0; i < readConfig.length; i++) {
        let readPlanLen = this.readPlan.filter(read => read.group === readConfig[i].group).length
        if (readPlanLen < readConfig[i].times) {
          await this.getReadPlan(readConfig[i].group, readConfig[i].times - readPlanLen)
        }
      }
    },
    // 核心计划添加功能part1
    async getReadPlan(group, times) {
      for (let i = 0; i < times; i++) {
        // 最后一次阅读记录 lastRecord
        let lastRecord = {}
        if (this.readPlan.length && this.readPlan.filter(item => item.group === group).length) {
          // 今日有阅读记录
          let findAll = this.readPlan.filter(item => {
            return item.group === group
          })
          lastRecord = findAll[findAll.length - 1]
          // lastRecord = this.readPlan.findLast(item => {
          //   return item.group === group
          // })
          this.pushReadPlan(group, lastRecord)
        } else {
          // 今日无阅读记录
          const {result: res} = await db.collection('read-log').orderBy('create_date desc').where({group: group}).limit(1).get()
          if (res.errCode === 0) {
            if (res.data && res.data.length) {
              lastRecord = res.data[0]
              // 获取到改组最后一次阅读记录 lastRecord
              this.pushReadPlan(group, lastRecord)
            } else {
              // 从来没有阅读该组
              let firstBook = this.books.find(book => {
                return book.group === group
              })
              this.readPlan.push(
                  {
                    name: firstBook.name,
                    chapter: 1,
                    group: firstBook.group,
                    date: '',
                    flag: firstBook.name + String(1),
                    code: firstBook.sn
                  },
              )
            }
          }
        }
      }
    },
    // 核心计划添加功能part2
    pushReadPlan(group, lastRecord) {
      // findBook:在所有books中找到最后一次阅读那卷书
      let findBook = this.books.find(book => {
        return book.name === lastRecord.name
      })
      //  [判断]是否读到本卷尾,找到该卷在整本书中的索引
      if (lastRecord.chapter === findBook.count) {
        let groupBooks = this.books.filter(book => book.group === group)
        // 当前组的总长度
        let curGroupLen = groupBooks.length
        // 本卷所在当前组的索引
        let groupIndex = groupBooks.findIndex(groupBook => {
          return groupBook.name === lastRecord.name
        })
        // [判断]是否读到当前组最后一卷
        if (curGroupLen === groupIndex + 1) {
          // 回到卷首
          let nextBook = groupBooks[0]
          this.readPlan.push(
              {
                name: nextBook.name,
                chapter: 1,
                group: nextBook.group,
                date: '',
                flag: nextBook.name + '1',
                code: nextBook.sn
              },
          )
        } else {
          // 往后+1卷
          let curIndex = this.books.filter(book => book.group === lastRecord.group).findIndex(book => {
            return book.name === lastRecord.name
          })
          let nextBook = this.books.filter(book => book.group === lastRecord.group)[curIndex + 1]
          this.readPlan.push(
              {
                name: nextBook.name,
                chapter: 1,
                group: nextBook.group,
                date: '',
                flag: nextBook.name + '1',
                code: nextBook.sn
              },
          )
        }
      } else {
        this.readPlan.push(
            {
              name: lastRecord.name,
              chapter: lastRecord.chapter + 1,
              group: lastRecord.group,
              date: '',
              flag: lastRecord.name + String(lastRecord.chapter + 1),
              code: lastRecord.code + 1
            },
        )
      }
    },
    // 选中日期
    dateSelect(e) {
      this.pointDate = e.value
      // 查看今天
      if (e.value === this.today) {
        if (!this.readPlan.length) {
          uni.showLoading({
            mask: true
          })
          db.collection('read-log').orderBy('create_date desc').where({date: e.value}).get().then(({result: res}) => {
            if (res.errCode === 0) {
              this.readPlan = res.data.reverse()
              uni.hideLoading()
              this.startPlan()
            }
          })
        }
      } else {
        if (this.readLogCache[this.pointDate]) {
          this.$set(this, "readLog", this.readLogCache[this.pointDate]);
        } else {
          // 查看非今天记录
          uni.showLoading({
            mask: true
          })
          db.collection('read-log').orderBy('create_date desc').where({date: e.value}).get().then(({result: res}) => {
            if (res.errCode === 0) {
              this.readLog = res.data.reverse()
              this.$set(this.readLogCache, this.pointDate, Object.assign({}, this.readLog));
              uni.hideLoading()
            }
          })
        }
      }
    },
    readCheck(e, flag) {
      let findObj = this.readPlan.find(item => item.flag === flag)
      if (e.checked) {
        findObj.date = this.today
        this.submitForm(findObj)
      }
    },
    submitForm(value) {
      let param = Object.assign({}, value);
      delete param.flag
      db.collection('read-log').add(param).then((res) => {
        console.log(res)
        if (res.result.errCode === 0) {
          this.fui.toast('打卡成功！')
        }
      }).catch((err) => {
        uni.showModal({
          content: err.message || '请求服务失败',
          showCancel: false
        })
      })
    }
  }
}
</script>

<style lang="scss">

.fui-list__item {

  display: flex;
  align-items: center;
  background-color: #FFFFFF;
  padding: 28rpx 32rpx;
  box-sizing: border-box;
  overflow-x: auto;
}

.fui-text {
  font-size: 30rpx;
  padding: 0 16px 0 16rpx;
  white-space: nowrap;
}

.fui-list__cell {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.group {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-right: 12px;
}

.fui-list {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.fui-list--item {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  margin-right: 24rpx;
}

.fui-list--item:last-child {
  margin-right: 0;
}

.play-code {
  position: absolute;
  left: 28px;
  bottom: -15px;
  font-size: 12px;
  color: #333333;
}
</style>
