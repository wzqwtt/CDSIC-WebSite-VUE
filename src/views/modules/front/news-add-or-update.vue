<template>
  <el-dialog
    :title="!dataForm.nid ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">

    <el-form-item label="新闻标题" prop="ntitle">
      <el-input v-model="dataForm.ntitle" placeholder="新闻标题"></el-input>
    </el-form-item>
        <el-form-item label="新闻简要介绍" prop="nsynopsis">
      <el-input v-model="dataForm.nsynopsis" placeholder="新闻简要介绍"></el-input>
    </el-form-item>
    <el-form-item label="新闻内容" prop="ncontent">
      <el-input v-model="dataForm.ncontent" placeholder="新闻内容"></el-input>
    </el-form-item>

    <el-form-item label="新闻发布时间" prop="ndate">
      <div class="block">
        <el-date-picker
          v-model="dataForm.ndate"
          type="date"
          placeholder="选择日期">
        </el-date-picker>
     </div>
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
          nid: 0,
          ndate: '',
          ntitle: '',
          ncontent: '',
          nsynopsis: ''
        },
        value1:'',
        dataRule: {
          ndate: [
            { required: true, message: '新闻时间不能为空', trigger: 'blur' }
          ],
          ntitle: [
            { required: true, message: '新闻标题不能为空', trigger: 'blur' }
          ],
          ncontent: [
            { required: true, message: '新闻内容不能为空', trigger: 'blur' }
          ],
          nsynopsis: [
            { required: true, message: '新闻简要介绍不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.nid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.nid) {
            this.$http({
              url: this.$http.adornUrl(`/front/news/info/${this.dataForm.nid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.ndate = data.news.ndate
                this.dataForm.ntitle = data.news.ntitle
                this.dataForm.ncontent = data.news.ncontent
                this.dataForm.nsynopsis = data.news.nsynopsis
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
              url: this.$http.adornUrl(`/front/news/${!this.dataForm.nid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'nid': this.dataForm.nid || undefined,
                'ndate': this.dataForm.ndate,
                'ntitle': this.dataForm.ntitle,
                'ncontent': this.dataForm.ncontent,
                'nsynopsis': this.dataForm.nsynopsis
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
