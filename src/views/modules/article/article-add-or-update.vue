<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="文章类型ID" prop="articleTypeid">
      <el-input v-model="dataForm.articleTypeid" placeholder="文章类型ID"></el-input>
    </el-form-item>
    <el-form-item label="标题" prop="articleTitle">
      <el-input v-model="dataForm.articleTitle" placeholder="标题"></el-input>
    </el-form-item>
    <el-form-item label="文章内容" prop="articleContent">
      <el-input v-model="dataForm.articleContent" placeholder="文章内容"></el-input>
    </el-form-item>
    <el-form-item label="发送时间" prop="articleSendtime">
      <el-input v-model="dataForm.articleSendtime" placeholder="发送时间"></el-input>
    </el-form-item>
    <el-form-item label="作者ID" prop="articleAuthid">
      <el-input v-model="dataForm.articleAuthid" placeholder="作者ID"></el-input>
    </el-form-item>
    <el-form-item label="文章阅读数" prop="articleCount">
      <el-input v-model="dataForm.articleCount" placeholder="文章阅读数"></el-input>
    </el-form-item>
    <el-form-item label="文章来源" prop="articleFrom">
      <el-input v-model="dataForm.articleFrom" placeholder="文章来源"></el-input>
    </el-form-item>
    <el-form-item label="文章信息" prop="articleInfo">
      <el-input v-model="dataForm.articleInfo" placeholder="文章信息"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          articleTypeid: '',
          articleTitle: '',
          articleContent: '',
          articleSendtime: '',
          articleAuthid: '',
          articleCount: '',
          articleFrom: '',
          articleInfo: ''
        },
        dataRule: {
          articleTypeid: [
            { required: true, message: '文章类型ID不能为空', trigger: 'blur' }
          ],
          articleTitle: [
            { required: true, message: '标题不能为空', trigger: 'blur' }
          ],
          articleContent: [
            { required: true, message: '文章内容不能为空', trigger: 'blur' }
          ],
          articleSendtime: [
            { required: true, message: '发送时间不能为空', trigger: 'blur' }
          ],
          articleAuthid: [
            { required: true, message: '作者ID不能为空', trigger: 'blur' }
          ],
          articleCount: [
            { required: true, message: '文章阅读数不能为空', trigger: 'blur' }
          ],
          articleFrom: [
            { required: true, message: '文章来源不能为空', trigger: 'blur' }
          ],
          articleInfo: [
            { required: true, message: '文章信息不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/bean/article/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.articleTypeid = data.article.articleTypeid
                this.dataForm.articleTitle = data.article.articleTitle
                this.dataForm.articleContent = data.article.articleContent
                this.dataForm.articleSendtime = data.article.articleSendtime
                this.dataForm.articleAuthid = data.article.articleAuthid
                this.dataForm.articleCount = data.article.articleCount
                this.dataForm.articleFrom = data.article.articleFrom
                this.dataForm.articleInfo = data.article.articleInfo
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/bean/article/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'articleTypeid': this.dataForm.articleTypeid,
                'articleTitle': this.dataForm.articleTitle,
                'articleContent': this.dataForm.articleContent,
                'articleSendtime': this.dataForm.articleSendtime,
                'articleAuthid': this.dataForm.articleAuthid,
                'articleCount': this.dataForm.articleCount,
                'articleFrom': this.dataForm.articleFrom,
                'articleInfo': this.dataForm.articleInfo
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
