<template>
  <a-modal
    :title="title"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭"
    wrapClassName="ant-modal-cust-warp"
    style="top:15%;height: 75%;overflow-y: hidden">
    <template slot="footer">
      <a-button key="back" v-if="isReadOnly" @click="handleCancel">
        关闭
      </a-button>
    </template>
    <a-spin :spinning="confirmLoading">
      <a-form :form="form" id="customerModal">
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="名称">
            <a-input placeholder="请输入名称" v-decorator.trim="[ 'supplier', validatorRules.supplier]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="联系人">
            <a-input placeholder="请输入联系人" v-decorator.trim="[ 'contacts' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="手机号码">
            <a-input placeholder="请输入手机号码" v-decorator.trim="[ 'telephone' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="联系电话">
            <a-input placeholder="请输入联系电话" v-decorator.trim="[ 'phoneNum' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="电子邮箱">
            <a-input placeholder="请输入电子邮箱" v-decorator.trim="[ 'email' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="传真">
            <a-input placeholder="请输入传真" v-decorator.trim="[ 'fax' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="期初应收">
            <a-input placeholder="请输入期初应收" v-decorator.trim="[ 'beginNeedGet' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="期末应收">
            <a-input v-decorator.trim="[ 'allNeedGet' ]" :readOnly="true" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="纳税人识别号">
            <a-input placeholder="请输入纳税人识别号" v-decorator.trim="[ 'taxNum' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="税率(%)">
            <a-input-number style="width:100%" placeholder="请输入税率" v-decorator.trim="[ 'taxRate' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="开户行">
            <a-input placeholder="请输入开户行" v-decorator.trim="[ 'bankName' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="账号">
            <a-input placeholder="请输入账号" v-decorator.trim="[ 'accountNumber' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="地址">
            <a-input placeholder="请输入地址" v-decorator.trim="[ 'address' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="排序">
            <a-input placeholder="请输入排序" v-decorator.trim="[ 'sort' ]" />
          </a-form-item>
        </a-col>
        <a-col :span="24/2">
          <a-form-item :labelCol="labelCol" :wrapperCol="wrapperCol" label="备注">
            <a-textarea :rows="2" placeholder="请输入备注" v-decorator.trim="[ 'description' ]" />
          </a-form-item>
        </a-col>
      </a-form>
    </a-spin>
  </a-modal>
</template>
<script>
  import pick from 'lodash.pick'
  import {addSupplier,editSupplier,checkSupplier } from '@/api/api'
  import {autoJumpNextInput} from "@/utils/util"
  export default {
    name: "CustomerModal",
    data () {
      return {
        title:"操作",
        visible: false,
        model: {},
        isReadOnly: false,
        labelCol: {
          xs: { span: 24 },
          sm: { span: 6 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
        confirmLoading: false,
        form: this.$form.createForm(this),
        validatorRules:{
          supplier:{
            rules: [
              { required: true, message: '请输入名称!' },
              { min: 2, max: 60, message: '长度在 2 到 60 个字符', trigger: 'blur' },
              { validator: this.validateSupplierName}
            ]
          }
        },
      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'supplier', 'contacts', 'telephone', 'email',
            'phoneNum', 'fax', 'beginNeedGet', 'allNeedGet', 'taxNum', 'taxRate',
            'bankName', 'accountNumber', 'address', 'sort', 'description'))
          autoJumpNextInput('customerModal')
        });
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let formData = Object.assign(this.model, values);
            if(this.model.beginNeedGet && this.model.beginNeedPay) {
              that.$message.warn("期初应收和期初应付不能同时输入");
              that.confirmLoading = false;
              return;
            }
            formData.type = "客户";
            let obj;
            if(!this.model.id){
              obj=addSupplier(formData);
            }else{
              obj=editSupplier(formData);
            }
            obj.then((res)=>{
              if(res.code === 200){
                that.$emit('ok');
              }else{
                that.$message.warning(res.data.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }
        })
      },
      handleCancel () {
        this.close()
      },
      validateSupplierName(rule, value, callback){
        let params = {
          name: value,
          type: '客户',
          id: this.model.id?this.model.id:0
        };
        checkSupplier(params).then((res)=>{
          if(res && res.code===200) {
            if(!res.data.status){
              callback();
            } else {
              callback("名称已经存在");
            }
          } else {
            callback(res.data);
          }
        });
      }
    }
  }
</script>
<style scoped>

</style>