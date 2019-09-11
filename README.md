# Element-el-upload-picture-card

照片墙+已上传图片

## 需求

- 开通电子合同时，照片墙**list-type="picture-card**上传图片
- 若被驳回，可以编辑信息(包括图片,即带出已上传的图片`urlArray`，可删除`deletePic`/查看`previewPic`；仍可以照片墙的形式上传图片`el-upload picture-card`)。

## 实现

1.图片展示包括：urlArray、el-upload picture-card，主要实现 urlArray 部分

2.鼠标移入`<div class='contract-img'>`,把当前图片的 index 放入`maskShowArray`,即显示当前图片的蒙层;鼠标移出`<div class='contract-img'>`,删除 index

3.鼠标移入`<i class="el-icon-zoom-in">`、`<i class="el-icon-delete">`(查看/删除图标),仍显示蒙层,即需在两标签绑定`imgEnter`

**html**

```html
<div class="img-wrap">
  <!-- 已上传的图片 -->
  <div class="contract-img" v-for="(u,index) in urlArray" :key="index" @mouseenter.stop="imgEnter(index)" @mouseleave.stop="imgLeave(index)">
    <img class="imgs" :src="u" />
    <div v-show="maskShowArray.includes(index)" class="mask">
      <i class="el-icon-zoom-in icon" @mouseenter.stop="imgEnter(index)" @click="previewPic(u,index)"></i>
      <i class="el-icon-delete icon" @mouseenter.stop="imgEnter(index)" @click="deletePic(index)"></i>
    </div>
  </div>
  <!-- 照片墙 -->
  <el-upload drag action list-type="picture-card" :multiple="true" :http-request="uploadImg" :on-preview="handlePreview" :on-remove="handleRemove" :limit="9-urlArray.length" name="license" accept="image/gif, image/jpeg, image/jpg, image/png">
    <i class="el-icon-plus"></i>
  </el-upload>
</div>
<el-dialog :visible.sync="dialogVisible" size="tiny">
  <img width="100%" :src="dialogImageUrl" alt />
</el-dialog>
```

**script**

```javascript
    data(){
        return {
            urlArray:[],        // 已上传的图片 电子合同详情接口返回数据

            maskShowArray: [],  // 需要显示的图片蒙层数组
            maskShow: false,    // 蒙层显示

            // 预览图片
            dialogImageUrl: '',
            dialogVisible: false,
        }
    },
    methods:{
        // 鼠标移入移出图片
        imgEnter(index) {
          if (this.maskShowArray.includes(index)) return
          this.maskShowArray.push(index)
        },
          imgLeave(index) {
          const i = this.maskShowArray.indexOf(index)
          this.maskShowArray.splice(i, 1)
        },
          // 删除图片
        deletePic(index) {
          this.urlArray.splice(index, 1)
        },
          // 预览图片
        previewPic(url, index) {
          this.dialogImageUrl =  url
          this.dialogVisible = true
          this.imgLeave(index)
        }
    }

```
