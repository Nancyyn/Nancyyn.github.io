---
title: 导出Excel
date: 2020-05-22 15:55:22
categories:
  - 导出Excel
tags:
  - 技术总结
---

### 导出 Excel

- 定义导出 api，注意：加上 `responseType: 'blob'`

```javascript
// 导出房间
export function exportRoom(params) {
  return request({
    url: '/api/resource/v2/rs/cf/rm/sel/export/excel',
    method: 'get',
    params,
    responseType: 'blob'
  })
}
```

- 在相应页面中调用 api

```javascript
// 导出房间
exportRoomFile() {
    exportRoom({ bid: this.buildingId }).then(res => {
        const link = document.createElement('a')
        link.href = window.URL.createObjectURL(new Blob([res]))
        link.download = this.buildingInfo.buildingName + '-房间数据.xlsx'
        document.body.appendChild(link)

        link.click()
        window.URL.revokeObjectURL(link.href)
        document.body.removeChild(link)
    })
}
```
