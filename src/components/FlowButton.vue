<template>
  <div class="flow_footer flow_footer_padding vux-1px-t animated slideInUp">
    <flexbox>
      <flexbox-item v-for="(btype, index) in buttonArr" v-if="index < moreButtonNum" :key="btype" style="margin-left: 0">
        <div class="flow_button" @click="clickButton(btype)">{{fbname.buttonName[btype]}}</div>
      </flexbox-item>
      <flexbox-item v-if="buttonArr.length > moreButtonNum" style="margin-left: 0">
        <div class="flow_button" @click="clickMoreButton(buttonArr)">更多</div>
      </flexbox-item>
    </flexbox>
  </div>
</template>

<script>
  import fbname from '@/flow/flowButtonNames'
  import {Divider, Flexbox, FlexboxItem} from 'vux'
  import {mapGetters} from 'vuex'

  export default {
    name: 'FlowButton',
    props: {
      buttonArr: {
        type: Array,
        default() {
          return []
        }
      },
      flowParams: {
        type: Object,
        default() {
          return {}
        }
      },
      handleInfos: {
        type: Object,
        default() {
          return {}
        }
      },
      zin: {
        type: String
      }
    },
    data() {
      return {
        moreButtonNum: 4,
        fbname: {}
      }
    },
    components: {
      Divider,
      Flexbox,
      FlexboxItem
    },
    created() {
      this.fbname = fbname
      let dd = window.dd
      dd.ready(function () {
        dd.device.base.getPhoneInfo({
          onSuccess: function(data) {
            console.log(data)
          },
          onFail: function(err) {}
        });
      })
    },
    computed: {
      ...mapGetters({
        editfieldinfo: 'editfieldinfo'
      })
    },
    methods: {
      clickButton(btype) {
        let flowParams = JSON.stringify(this.flowParams);
        const selectPerson = JSON.stringify(this.flowParams.selectPerson);
        const flag = this.flag;
        const zin = this.zin;
        let editFlag = this.editfieldinfo; // 需要补填字段
        if (editFlag.length > 0 && btype === 'tj') {
          this.$router.push({
            path: '/flowEdit',
            query: {btype: btype, flowParams: flowParams, selectPerson: selectPerson, flag: flag, zin: zin}
          })
        } else {
          this.$router.push({
            path: '/flowIdea',
            query: {btype: btype, flowParams: flowParams, selectPerson: selectPerson, flag: flag, zin: zin}
          })
        }
//        this.$emit('on-fb-click')
      },
      clickMoreButton(buttonArr) {
        let otherButtons = []
        for (let btnIndex in buttonArr) {
          if (btnIndex >= this.moreButtonNum) {
            otherButtons.push(fbname.buttonName[buttonArr[btnIndex]])
          }
        }
        let dd = window.dd;
        let _that = this;
        dd.device.notification.actionSheet({
          title: '更多操作',
          cancelButton: '取消',
          otherButtons: otherButtons,
          onSuccess: function(result) {
            if (result.buttonIndex !== -1) { // 取消
              let btype = fbname.buttonKey[otherButtons[result.buttonIndex]]
              _that.clickButton(btype)
            }
          },
          onFail: function(err) {}
        })
      }
    }
  }
</script>

<style scoped lang="less">
  @import '~vux/src/styles/1px.less';

  .flow_footer {
    width: 100%;
    /*height: @flow_button_footer_height;*/
    /*line-height: @flow_button_footer_height;*/
    padding-top: 10px;
    padding-bottom: 20px;
    position: fixed;
    bottom: 0;
    background-color: @flow_button_footer_background_color;
  }

  @media only screen and (width: 375px) and (height: 690px) {
    .flow_footer_padding {
      padding-bottom: constant(safe-area-inset-bottom);
    }
  }

  .flow_button {
    text-align: center;
    font-size: @flow_button_font_size;
    color: @flow_button_color;
  }
</style>
