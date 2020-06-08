<template>
  <div>
    <component :is="loadertpl"
               :DataObj="dialogObj.data"
               :editTitle="editTitle"></component>

  </div>
</template>

<script>
export default {
  data() {
    return {
      dialogObj: {}
    }
  },
  computed: {
    loadertpl() {
      if (this.dialogObj.component) {
        return () => import('@/components/dialog/' + this.dialogObj.component + '.vue')
      } else {
        return null;
      }
    }
  },
  methods: {
    init: function () {
      const self = this;
      self.loadertpl().then(() => {
        // 动态加载模板
        self.rendertpl = () => self.loadertpl();
      }).catch(() => {
        // 模板不存在404时处理
        //self.rendertpl = () => import('@/components/library/public/404/');
      })
    },
    initData() {
      // 再增加动态传值Style !!!
      
      let _self = this;
      _self.$bus.$on("ivDialogShow", function (ret) {
        _self.AddDialogList(ret);
      })
    },
    AddDialogList(ret) {
      this.dialogObj = {};
      //传入component、data
      this.$nextTick(function () {
        let obj = JSON.parse(JSON.stringify(ret));
        //obj['id'] = 'dialog-' + this.guid();
        obj['dialogVisible'] = true;
        this.dialogObj = obj;
      })
    },
    CloseDialog() {
      this.dialogObj = {};
      /* item.dialogVisible = false;
      let index = this.dialogList.indexOf(item);
      this.dialogList.splice(index, 1); */
    },
    guid() {
      return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
        var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
        return v.toString(16);
      });
    },
    editTitle(title, subtitle) {
      if (title) {
        this.dialogObj.title = title;
      }
      if (title) {
        this.dialogObj.subtitle = subtitle;
      }
    }
  },
  mounted() {
    this.initData();
  }
}
</script>

<style lang="scss">
.iv-dialog {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 999;
  background: transparent;
}
</style>>
