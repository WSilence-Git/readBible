<template>
  <view>
    <fui-status-bar background="#fff"></fui-status-bar>
    <fui-calendar @change="dateSelect" :isMultiple="false" :value="today"></fui-calendar>

    <!--    阅读记录-->
    <view v-if="pointDate !== today">
      <view v-for="(group,index) in groups" :key="index">
        <fui-checkbox-group name="checkbox">
          <view class="fui-list__item">
            <fui-label v-for="(item,iindex) in todayFilter(readLog)" :key="iindex">
              <view class="fui-align__center" v-if="item.group===group">
                <fui-checkbox :disabled="true" :checked="true" :value="item.value" borderRadius="8rpx"></fui-checkbox>
                <text class="fui-text">{{ `${item.name} ${item.chapter}` }}</text>
              </view>
            </fui-label>
          </view>
        </fui-checkbox-group>
      </view>
    </view>
    <!--    阅读计划-->
    <view v-if="pointDate === today">
      <view v-for="(group,index) in groups" :key="index" class="group">
        <fui-checkbox-group name="checkbox">
          <view class="fui-list__item" style="width: 300px;">
            <fui-label v-for="(item,iindex) in readPlan" :key="iindex" style="display: inline-flex">
              <view class="fui-align__center" v-if="item.group===group">
                <fui-checkbox
                    :disabled="!!item.date" :checked="!!item.date" :value="item.value"
                    borderRadius="8rpx" color="#09be4f"
                    @change="readCheck($event,item.flag)">
                </fui-checkbox>
                <text class="fui-text">{{ `${item.name} ${item.chapter}` }}</text>
              </view>
            </fui-label>
          </view>
        </fui-checkbox-group>
        <view class="tag-wrap">
          <fui-tag text="+1" size="26" @click="pushReadPlan(group,1)" type="success"></fui-tag>
        </view>
      </view>
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
        },
        {
          name: '出', //出
          count: 40,
          group: 'A',
        },
        {
          name: '利',
          count: 27,
          group: 'A',
        },
        {
          name: '民',
          count: 36,
          group: 'A',
        },
        {
          name: '申',
          count: 34,
          group: 'A',
        },
        {
          name: '书',
          count: 24,
          group: 'A',
        },
        {
          name: '士',
          count: 21,
          group: 'A',
        },
        {
          name: '得',
          count: 4,
          group: 'A',
        },
        {
          name: '撒上',
          count: 31,
          group: 'A',
        },
        {
          name: '撒下',
          count: 24,
          group: 'A',
        },
        {
          name: '王上',
          count: 22,
          group: 'A',
        },
        {
          name: '王下',
          count: 25,
          group: 'A',
        },
        {
          name: '代上',
          count: 29,
          group: 'A',
        },
        {
          name: '代下',
          count: 36,
          group: 'A',
        },
        {
          name: '拉',
          count: 10,
          group: 'A',
        },
        {
          name: '尼',
          count: 13,
          group: 'A',
        },
        {
          name: '斯',
          count: 10,
          group: 'A',
        },
        {
          name: '伯',
          count: 42,
          group: 'A',
        },
        {
          name: '诗',
          count: 150,
          group: 'D',
        },
        {
          name: '箴',
          count: 31,
          group: 'C',
        },
        {
          name: '传',
          count: 12,
          group: 'A',
        },
        {
          name: '歌',
          count: 8,
          group: 'A',
        },
        {
          name: '赛',
          count: 66,
          group: 'A',
        },
        {
          name: '耶',
          count: 52,
          group: 'A',
        },
        {
          name: '哀',
          count: 5,
          group: 'A',
        },
        {
          name: '结',
          count: 48,
          group: 'A',
        },
        {
          name: '但',
          count: 12,
          group: 'A',
        },
        {
          name: '何',
          count: 14,
          group: 'A',
        },
        {
          name: '珥',
          count: 3,
          group: 'A',
        },
        {
          name: '摩',
          count: 9,
          group: 'A',
        },
        {
          name: '俄',
          count: 1,
          group: 'A',
        },
        {
          name: '拿',
          count: 4,
          group: 'A',
        },
        {
          name: '弥',
          count: 7,
          group: 'A',
        },
        {
          name: '鸿',
          count: 3,
          group: 'A',
        },
        {
          name: '哈',
          count: 3,
          group: 'A',
        },
        {
          name: '番',
          count: 3,
          group: 'A',
        },
        {
          name: '该',
          count: 2,
          group: 'A',
        },
        {
          name: '亚',
          count: 14,
          group: 'A',
        },
        {
          name: '玛',
          count: 4,
          group: 'A',
        },
        {
          name: '太', //太
          count: 28,
          group: 'B',
        },
        {
          name: '可',
          count: 16,
          group: 'B',
        },
        {
          name: '路',
          count: 24,
          group: 'B',
        },
        {
          name: '约',
          count: 21,
          group: 'B',
        },
        {
          name: '徒',
          count: 28,
          group: 'B',
        },
        {
          name: '罗',
          count: 16,
          group: 'B',
        },
        {
          name: '林前',
          count: 16,
          group: 'B',
        },
        {
          name: '林后',
          count: 13,
          group: 'B',
        },
        {
          name: '加',
          count: 6,
          group: 'B',
        },
        {
          name: '弗',
          count: 6,
          group: 'B',
        },
        {
          name: '腓',
          count: 4,
          group: 'B',
        },
        {
          name: '西',
          count: 4,
          group: 'B',
        },
        {
          name: '帖前',
          count: 5,
          group: 'B',
        },
        {
          name: '帖后',
          count: 3,
          group: 'B',
        },
        {
          name: '提前',
          count: 6,
          group: 'B',
        },
        {
          name: '提后',
          count: 4,
          group: 'B',
        },
        {
          name: '多',
          count: 3,
          group: 'B',
        },
        {
          name: '门',
          count: 1,
          group: 'B',
        },
        {
          name: '来',
          count: 13,
          group: 'B',
        },
        {
          name: '雅',
          count: 5,
          group: 'B',
        },
        {
          name: '彼前',
          count: 5,
          group: 'B',
        },
        {
          name: '彼后',
          count: 3,
          group: 'B',
        },
        {
          name: '约壹',
          count: 5,
          group: 'B',
        },
        {
          name: '约贰',
          count: 1,
          group: 'B',
        },
        {
          name: '约叁',
          count: 1,
          group: 'B',
        },
        {
          name: '犹',
          count: 1,
          group: 'B',
        },
        {
          name: '启',
          count: 22,
          group: 'B',
        },
      ],
      readLog: [],
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
      uni.showLoading({
        mask: true
      })
      db.collection('read-log').orderBy('create_date desc').limit(50).get().then(({result: res}) => {
        if (res.errCode === 0) {
          this.readLog = res.data.reverse()
          this.pointDate = this.today
          let todayLog = this.readLog.filter(item => item.date === this.today)
          this.readPlan.push(...todayLog)
          uni.hideLoading()
          this.startPlan()
        }
      })
    },
    // 执行配置
    startPlan() {
      let readConfig = [
        {group: 'A', times: 2},
        {group: 'B', times: 2},
        {group: 'C', times: 1},
        {group: 'D', times: 4}
      ]
      readConfig.forEach(item => {
        let readPlanLen = this.readPlan.filter(read => read.group === item.group).length
        if (readPlanLen < item.times) {
          this.pushReadPlan(item.group, item.times - readPlanLen)
        }
      })
    },
    // 核心计划添加功能
    pushReadPlan(group, times) {
      for (let i = 0; i < times; i++) {
        let lastRecord = {}
        if (this.readPlan.length && this.readPlan.filter(item => item.group === group).length) {
          let findAll = this.readPlan.filter(item => {
            return item.group === group
          })
          lastRecord = findAll[findAll.length - 1]
          // lastRecord = this.readPlan.findLast(item => {
          //   return item.group === group
          // })
        } else {
          let findAll = this.readLog.filter(item => {
            return item.group === group
          })
          lastRecord = findAll[findAll.length - 1]
        }

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
                  flag: nextBook.name + '1'
                },
            )
          } else {
            // 往后+1卷
            let curIndex = this.books.findIndex(book => {
              return book.name === lastRecord.name
            })
            let nextBook = this.books[curIndex + 1]
            this.readPlan.push(
                {
                  name: nextBook.name,
                  chapter: 1,
                  group: nextBook.group,
                  date: '',
                  flag: nextBook.name + '1'
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
                flag: lastRecord.name + String(lastRecord.chapter + 1)
              },
          )
        }
      }
    },
    dateSelect(e) {
      this.pointDate = e.value
    },
    todayFilter(arr) {
      return arr.filter(item => item.date === this.pointDate)
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
</style>
