<template>

  <el-dialog
    :title="!dataForm.pid ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="150px">

    <el-form-item label="成果标题" prop="ptitle">
      <el-input v-model="dataForm.ptitle" placeholder="成果标题"></el-input>
    </el-form-item>
    <el-form-item label="成果代表性图片" prop="pimage">
      <!-- <el-input v-model="dataForm.pimage" placeholder="成果代表性图片"></el-input> -->
      <single-upload v-model="dataForm.pimage">

      </single-upload>
    </el-form-item>
    <el-form-item label="成果发表平台" prop="pplatform">
      <el-input v-model="dataForm.pplatform" placeholder="成果发表平台"></el-input>
    </el-form-item>
    <el-form-item label="成果参与研究人员" prop="pperson">
      <el-input v-model="dataForm.pperson" placeholder="成果参与研究人员"></el-input>
    </el-form-item>
    <el-form-item label="成果简要介绍" prop="psynopsis">
      <el-input v-model="dataForm.psynopsis" placeholder="成果简要介绍"></el-input>
    </el-form-item>
    <el-form-item label="成果主要内容" prop="pcontent">
      <el-input v-model="dataForm.pcontent" placeholder="成果主要内容"></el-input>
    </el-form-item>
    <el-form-item label="是否在轮播图中展示" prop="piscarousel">
      <!-- <el-input v-model="dataForm.piscarousel" placeholder="成果是否在轮播图中展示"></el-input> -->
        <el-switch
          v-model="dataForm.piscarousel"
          active-color="#13ce66"
          inactive-color="#ff4949"
          :active-value="1"
          :inactive-value="0">
        </el-switch>
    </el-form-item>
    <el-form-item label="成果发布时间" prop="pdate">
      <el-input v-model="dataForm.pdate" placeholder="成果发布时间"></el-input>
    </el-form-item>
    <!-- <el-form-item label="如果展示轮播图，这里写入顺序，默认不展示（值为-1）" prop="porder">
      <el-input v-model="dataForm.porder" placeholder="如果展示轮播图，这里写入顺序，默认不展示（值为-1）"></el-input>
    </el-form-item> -->
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
  
</template>

<script>
  import singleUpload from "@/components/upload/singleUpload"

  export default {
  components: { singleUpload },
    data () {
      return {
        visible: false,
        dataForm: {
          pid: 0,
          pdate: '',
          ptitle: '',
          pimage: '',
          pplatform: '',
          pperson: '',
          pcontent: '',
          psynopsis: '',
          piscarousel: '',
          porder: ''
        },
        dataRule: {
          pdate: [
            { required: true, message: '成果发布时间不能为空', trigger: 'blur' }
          ],
          ptitle: [
            { required: true, message: '成果标题不能为空', trigger: 'blur' }
          ],
          pimage: [
            { required: true, message: '成果代表性图片不能为空', trigger: 'blur' }
          ],
          pplatform: [
            { required: true, message: '成果发表平台不能为空', trigger: 'blur' }
          ],
          pperson: [
            { required: true, message: '成果参与研究人员不能为空', trigger: 'blur' }
          ],
          pcontent: [
            { required: true, message: '成果主要内容不能为空', trigger: 'blur' }
          ],
          psynopsis: [
            { required: true, message: '成果简要介绍不能为空', trigger: 'blur' }
          ],
          piscarousel: [
            { required: true, message: '成果是否在轮播图中展示不能为空', trigger: 'blur' }
          ]
          // ,
          // porder: [
          //   { required: true, message: '如果展示轮播图，这里写入顺序，默认不展示（值为-1）不能为空', trigger: 'blur' }
          // ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.pid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.pid) {
            this.$http({
              url: this.$http.adornUrl(`/front/project/info/${this.dataForm.pid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.pdate = data.project.pdate
                this.dataForm.ptitle = data.project.ptitle
                this.dataForm.pimage = data.project.pimage
                this.dataForm.pplatform = data.project.pplatform
                this.dataForm.pperson = data.project.pperson
                this.dataForm.pcontent = data.project.pcontent
                this.dataForm.psynopsis = data.project.psynopsis
                this.dataForm.piscarousel = data.project.piscarousel
                this.dataForm.porder = data.project.porder
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
              url: this.$http.adornUrl(`/front/project/${!this.dataForm.pid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'pid': this.dataForm.pid || undefined,
                'pdate': this.dataForm.pdate,
                'ptitle': this.dataForm.ptitle,
                'pimage': this.dataForm.pimage,
                'pplatform': this.dataForm.pplatform,
                'pperson': this.dataForm.pperson,
                'pcontent': this.dataForm.pcontent,
                'psynopsis': this.dataForm.psynopsis,
                'piscarousel': this.dataForm.piscarousel,
                'porder': this.dataForm.porder
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
