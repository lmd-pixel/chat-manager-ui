<template>
  <a-modal
    :title="modalTitle"
    :width="800"
    :visible="visible"
    :confirmLoading="confirmLoading"
    @ok="handleSubmit"
    @cancel="handleCancel"
  >
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-form-item
          label="业务ID"
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          v-show="false"
        >
          <a-input
            v-if="modalOpera == 1"
            v-decorator="['id', { rules: [{ required: true }] }]"
          />
        </a-form-item>
        <a-form-item
          label="父级编号"
          :labelCol="labelCol"
          :wrapperCol="wrapperCol"
          v-show="false"
        >
          <a-input
            v-decorator="[
              'parentId',
              {
                rules: [{ required: false, message: '请输入！' }],
                initialValue: '0',
              },
            ]"
          />
        </a-form-item>
        <a-row :gutter="12">
          <a-col :xl="{ span: 12 }" :lg="{ span: 12 }" :md="{ span: 24 }">
            <a-form-item
              label="字典名"
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
            >
              <a-input
                v-decorator="[
                  'name',
                  {
                    rules: [
                      {
                        required: true,
                        min: 1,
                        message: '请输入至少1个字符的规则！',
                      },
                    ],
                  },
                ]"
              />
            </a-form-item>
          </a-col>
          <a-col :xl="{ span: 12 }" :lg="{ span: 12 }" :md="{ span: 24 }">
            <a-form-item
              label="字典值"
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
            >
              <a-input
                style="width: 100%"
                v-decorator="[
                  'value',
                  {
                    rules: [
                      {
                        required: true,
                        message: '请输入至少1个字符的规则！',
                      },
                    ],
                  },
                ]"
              />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="12">
          <a-col :xl="{ span: 12 }" :lg="{ span: 12 }" :md="{ span: 24 }">
            <a-form-item
              label="类型"
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
            >
              <a-input
                v-decorator="[
                  'type',
                  {
                    rules: [
                      {
                        required: true,
                        min: 3,
                        message: '请输入至少三个字符的规则！',
                      },
                    ],
                  },
                ]"
              />
            </a-form-item>
          </a-col>
          <a-col :xl="{ span: 12 }" :lg="{ span: 12 }" :md="{ span: 24 }">
            <a-form-item
              label="顺序"
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
            >
              <a-input
                :min="1"
                :max="1000"
                style="width: 100%"
                v-decorator="[
                  'num',
                  {
                    rules: [{ required: true, message: '请输入顺序' }],
                    initialValue: 1,
                  },
                ]"
              />
            </a-form-item>
          </a-col>
        </a-row>
        <a-row :gutter="12">
          <a-col :xl="{ span: 12 }" :lg="{ span: 12 }" :md="{ span: 24 }">
            <a-form-item
              label="描述"
              :labelCol="labelCol"
              :wrapperCol="wrapperCol"
            >
              <a-input
                v-decorator="[
                  'description',
                  {
                    rules: [
                      {
                        required: true,
                        min: 3,
                        message: '请输入至少三个字符的规则！',
                      },
                    ],
                  },
                ]"
              />
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
    </a-spin>
  </a-modal>
</template>

<script>
import { save, getById, update } from "@/api/system/dict";
import pick from "lodash.pick";
export default {
  data() {
    return {
      labelCol: {
        xs: { span: 24 },
        sm: { span: 7 },
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 13 },
      },
      modalTitle: "",
      modalOpera: 0, // 0：新增 1：编辑
      visible: false,
      confirmLoading: false,
      form: this.$form.createForm(this),
    };
  },
  created() {},
  methods: {
    add() {
      this.modal("新建数据字典", 0);
    },
    addChild(record) {
      console.log(record);
      this.add();
      setTimeout(() => {
        this.form.setFieldsValue({
          type: record.attributes.dictType,
          parentId: record.id,
          description: record.attributes.dictDesc,
        });
      }, 200);
    },
    edit(record) {
      this.modal("编辑数据字典", 1);
      getById({ pk: record.id }).then((response) => {
        this.form.setFieldsValue(
          pick(
            response.res,
            "id",
            "name",
            "value",
            "type",
            "description",
            "num",
            "parentId"
          )
        );
      });
    },
    modal(title, opera) {
      this.modalTitle = title;
      this.modalOpera = opera;
      this.visible = true;
      this.form.resetFields();
    },
    handleSubmit() {
      const {
        form: { validateFields },
      } = this;
      this.confirmLoading = true;
      validateFields((errors, values) => {
        if (!errors) {
          if (this.modalOpera === 0) {
            save(values).then((response) => {
              if (response.code === 0) {
                this.$message.info(response.message);
                this.$emit("ok");
                this.handleCancel();
              } else {
                this.$message.error(response.message);
              }
            });
          } else if (this.modalOpera === 1) {
            update(values).then((response) => {
              if (response.code === 0) {
                this.$message.info(response.message);
                this.$emit("ok");
                this.handleCancel();
              } else {
                this.$message.error(response.message);
              }
            });
          }
        }
        this.confirmLoading = false;
      });
    },
    handleCancel() {
      this.visible = false;
      this.confirmLoading = false;
    },
  },
};
</script>
