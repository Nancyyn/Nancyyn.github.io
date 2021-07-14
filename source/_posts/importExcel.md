---
title: 导入Excel
date: 2020-05-19 15:55:22
categories:
  - 导入Excel
tags:
  - 技术总结
---

### 导入 Excel

图一：上传文件

![uploadFiles](uploadFiles.png)

<!--more-->

图二：校验数据

![calibrationData](calibrationData.png)

图三：导入失败

![uploadFail](uploadFail.png)

图四：导入成功

![uploadSuccess](uploadSuccess.png)

- [图一页面：importModel.vue，点击链接后打开笔记可下载相应 vue 文件](https://note.youdao.com/s/65j02cuj)
- [下载模板页面：ExportButton.vue，点击链接后打开笔记可下载相应 vue 文件](https://note.youdao.com/s/VThlrNyi)

引用 importModel.vue 示例：

```javascript
<!-- 导入房间 -->
<import-model
  v-if="importRoomVisible"
  :visible.sync="importRoomVisible"
  title="导入房间数据"
  :import-loading="importRoomLoading"
  :file-url="roomFileUrl"
  :headers="roomHeader"
  :submit-data="addRoomData"
  :error-data="roomErrorData"
/>

<script>
const roomHeader = {
  floorName: {
    label: '楼层名称',
    width: '120'
  },
  roomNum: {
    label: '房间号',
    width: '120'
  },
  anotherName: {
    label: '房间别名',
    width: '120'
  },
  rnRlid: {
    label: '房间性质',
    minWidth: '200'
  },
  isPublic: {
    label: '是否公开',
    width: '100'
  },
  roomArea: {
    label: '房间建筑面积',
    width: '120'
  },
  insideArea: {
    label: '套内建筑面积',
    width: '120'
  }
}
import ImportModel from '@/components/ImportModel'
import { importRoom } from '@/api/resource'
export default {
  data() {
    return {
      importRoomVisible: false, // 导入房间
      floorErrorData: {}, // 楼层导入错误信息
      roomHeader, // 校验数据时生成表格所用
      roomFileUrl:
        '/incubatorpub/excel/%E6%88%BF%E9%97%B4%E6%95%B0%E6%8D%AE%E6%89%B9%E9%87%8F%E5%AF%BC%E5%85%A5%E6%A8%A1%E6%9D%BF.xlsx?Content-Disposition=attachment%3B%20filename%3D%22excel%2F%E6%88%BF%E9%97%B4%E6%95%B0%E6%8D%AE%E6%89%B9%E9%87%8F%E5%AF%BC%E5%85%A5%E6%A8%A1%E6%9D%BF.xlsx%22&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=scienceplatform%2F20210708%2F%2Fs3%2Faws4_request&X-Amz-Date=20210708T060102Z&X-Amz-Expires=432000&X-Amz-SignedHeaders=host&X-Amz-Signature=79c86f2d817f2f59a738ba582752cd641f4003ff289b508303196edb5012a452', // 房间模板下载地址
      importRoomLoading: false,
      roomErrorData: {} // 房间导入错误信息
    }
  },
  methods: {
    // 导入房间 emit
    importRoom() {
      this.importRoomVisible = true
    },
    // 批量导入房间
    addRoomData(data) {
      const activeBuildingId = this.editableTabs.filter(
        ({ name }) => name === this.editableTabsValue
      )[0].id
      this.importRoomLoading = true
      data.map((item) => {
        Object.keys(item).map((i) => {
          if (!item[i]) {
            item[i] = null
          }
        })
      })
      importRoom({ bid: activeBuildingId, list: data }).then((res) => {
        if (!res.code) {
          if (!res.data.code) {
            this.importRoomLoading = false
            this.roomErrorData = res.data
            this.$refs.floor[0].getFloorInfo() // 刷新房间列表
          } else {
            this.$message({
              message: res.data.msg,
              type: 'error'
            })
          }
        }
      })
    }
  }
}
</script>
```
