# import-and-export-excel
## excel的导入导出

安装

`npm i xlsx@0.14.1 file-saver@2.0.1`       

### 导出

使用

```js
import { pick } from 'lodash'
methods:{
    onExport() {
      import('@/vendor/Export2Excel').then(async excel => {
        const { rows } = await getEmployeeListAPI({ page: 1, size: this.page.total })
        console.log(rows)
        const headers = {
          '姓名': 'username',
          '手机号': 'mobile',
          '入职日期': 'timeOfEntry',
          '聘用形式': 'formOfEmployment',
          '转正日期': 'correctionTime',
          '工号': 'workNumber',
          '部门': 'departmentName'
        }
        const header = Object.keys(headers)
        const data = rows.map(t => {
          const m = pick(t, Object.values(headers))
          m.timeOfEntry = formatDate(m.timeOfEntry)
          m.correctionTime = formatDate(m.correctionTime)
          m.formOfEmployment = formatOfEmployment(m.formOfEmployment)
          return Object.values(m)
        })
        excel.export_json_to_excel({
          header,
          data,
          filename: 'excel-list',
          autoWidth: true,
          bookType: 'xlsx'
        })
      })
    },
}
```

### 导入

components/index.vue

```js
import UploadExcel from './UploadExcel'
export default {
  install(Vue) {
    Vue.component('UploadExcel', UploadExcel) // 注册导入excel组件
  }
}
```

src/views/import/index.vue

```js
<template>
  <!-- 公共导入组件 --> 
  <upload-excel :on-success="success" />
</template>

methods: {
    async onSuccess({ header, results }) {
      const userRelations = {
        '入职日期': 'timeOfEntry',
        '手机号': 'mobile',
        '姓名': 'username',
        '转正日期': 'correctionTime',
        '工号': 'workNumber'
      }
      const data = results.map(t => {
        const item = {}
        for (const key in t) {
          const newKey = userRelations[key]
          item[newKey] = t[key]
        }
        return item
      })
      await importEmployeeAPI(data)
      this.$message.success('操作成功')
    }
  }
```
