<template>
  <div class="opning_words">
    <a-page-header
      style="background-color: #fff;"
      title="Title"
      sub-title=""
    />
    <div class="tab-page-content">
      <div class="search">
        <a-form layout="inline">
          <a-row :gutter="24">
            <a-col :span="6">
              <a-form-item label="标题">
                <a-input v-model="search_parameter.title" placeholder="请输入" />
              </a-form-item>
            </a-col>
            <a-col :span="6">
              <a-form-item label="内容">
                <a-input v-model="search_parameter.content" placeholder="请输入" />
              </a-form-item>
            </a-col>
            <a-col :span="6">
              <a-form-item label="创建人">
                <a-input v-model="search_parameter.name" placeholder="请输入" />
              </a-form-item>
            </a-col>
            <a-col :span="6">
              <a-form-item>
                <a-button type="primary" @click="search_click">查询</a-button>
                <a-button style="margin-left: 8px" @click="search_clear">重置</a-button>
              </a-form-item>
            </a-col>
          </a-row>
        </a-form>
      </div>
      <div style="margin-top: 20px;background: #fff;">
        <div style="display: flex;padding: 15px 32px;border-bottom: 1px solid #d9d9d9;">
          <h3 style="line-height: 32px;font-size: 18px;margin-bottom: 0;">标题</h3>
          <div style="flex-grow: 1;"></div>
          <a-button @click="add" type="primary" icon="plus">
            添加
          </a-button>
        </div>
        <div style="padding: 10px 32px 20px 32px;">
          <a-table
            :rowKey="record => record.m"
            :columns="table_columns"
            :data-source="table_data"
            :loading="table_loading"
            :pagination="false"
          >
            <template slot="operation" slot-scope="record">
              <div>
                <a @click="poer_delete(record)">删除</a>
                <a-divider type="vertical" />
                <a @click="revise(record)">修改</a>
              </div>
            </template>
          </a-table>
        </div>
      </div>
    </div>
    <a-modal
      v-if="add_revise.visible"
      :title="add_revise.title"
      :visible="add_revise.visible"
      :confirm-loading="add_revise.loading"
      width="600px"
      @ok="handleOk"
      @cancel="handleCancel"
    >
      <a-form
        :form="add_revise.form"
        layout="horizontal"
        :label-col="{ span: 4 }"
        :wrapper-col="{ span: 18 }"
      >
        <a-form-item label="是否启用">
          <a-radio-group
            placeholder="请选择"
            v-decorator="[
              'documentStatus',
              {
                rules: [{ required: true, message: '是否启用不能为空!' }],
              },
            ]"
          >
            <a-radio value="0">启用</a-radio>
            <a-radio value="1">不启用</a-radio>
          </a-radio-group>
        </a-form-item>
        <a-form-item label="标题">
          <a-input
            placeholder="请输入"
            v-decorator="[
              'documentTitle',
              {
                rules: [{ required: true, message: '标题不能为空!' },{max:15,message:'输入标题文字超长，请重输入'}],
              },
            ]"
          />
        </a-form-item>
        <a-form-item label="内容">
          <a-textarea
            placeholder="请输入"
            :max-length="200"
            v-decorator="[
              'documentContent',
              {
                rules: [{ required: true, message: '内容不能为空!' },{max:200,message:'不能超过500字'}],
              },
            ]"
            :auto-size="{ minRows: 4, maxRows: 6 }"
          />
        </a-form-item>
        <a-form-item label="显示顺序">
          <a-select
            placeholder="请选择"
            v-decorator="[
              'documentSeq',
              {
                rules: [{ required: true, message: '显示顺序不能为空!' }]
              }
            ]"
          >
            <a-select-option
              v-for="item in carSeriesSelect"
              :key="item.customSeriesCode"
              :value="item.customSeriesCode"
            >{{`第${item.seriesNameCn}行`}}</a-select-option>
          </a-select>
        </a-form-item>
      </a-form>
    </a-modal>
  </div>
</template>

<script>
export default {
  name: "opning_words",
  components: {
  },
  data() {
    return {
      table_columns: [
        {
          title: "类型",
          key: "documentCategory",
          id: "documentCategory",
          dataIndex: 'documentCategory',
          checkedSwitch: true,
          // scopedSlots: { customRender: "custMobile" },
          align: 'left'
        },
        {
          title: "标题",
          key: "documentTitle",
          id: "documentTitle",
          dataIndex: 'documentTitle',
          checkedSwitch: true,
          // customRender: table_custom_null_change,
          // scopedSlots: { customRender: "custTelephone" },
          align: 'left'
        },
        {
          // customSeriesCode seriesYear
          title: "内容",
          key: "documentContent",
          id: "documentContent",
          dataIndex: 'documentContent',
          checkedSwitch: true,
          width: 200,
          // customRender: table_custom_null_change,
          // scopedSlots: { customRender: "seriesName" },
          align: 'left'
        },
        {
          // customModelCode modelYear
          title: "创建人",
          key: "createByName",
          id: "createByName",
          dataIndex: 'createByName',
          checkedSwitch: true,
          // customRender: table_custom_null_change,
          // scopedSlots: { customRender: "carModelName" },
          align: 'left'
        },
         {
          title: "创建时间",
          key: "createTime",
          id: "createTime",
          dataIndex: 'createTime',
          checkedSwitch: true,
          // customRender: table_columns_time_change,
          // scopedSlots: { customRender: "srcTypeName" },
          align: 'left'
        },
        {
          title: "更新时间",
          key: "updateTime",
          id: "updateTime",
          dataIndex: 'updateTime',
          checkedSwitch: true,
          // customRender: table_columns_time_change,
          // scopedSlots: { customRender: "channelName" },
          align: 'left'
        },
        {
          title: "显示顺序",
          key: "documentSeq",
          id: "documentSeq",
          dataIndex: 'documentSeq',
          checkedSwitch: true,
          // customRender: (value, row, index) => {
          //   return `第${value}行`
          // },
          // scopedSlots: { customRender: "channelName" },
          align: 'left'
        },
        {
          title: "状态",
          key: "documentStatus",
          id: "documentStatus",
          dataIndex: 'documentStatus',
          checkedSwitch: true,
          // customRender: (value, row, index) => {
          //   return value==='0' ? '已启用' : '未启用'
          // },
          // scopedSlots: { customRender: "channelName" },
          align: 'left'
        },
        {
          title: "操作",
          id: "operation",
          checkedSwitch: true,
          key: "operation",
          scopedSlots: {
            customRender: "operation",
          },
          align: "left",
        }
      ],
      table_data: new Array(),
      table_loading: true,
      add_revise: {
        title: String(),
        visible: false,
        loading: false,
        type: Number(),
        parameter: Object(),
        form: this.$form.createForm(this)
      },
      carSeriesSelect: [
        {customSeriesCode: 1,seriesNameCn: 1},
        {customSeriesCode: 2,seriesNameCn: 2},
        {customSeriesCode: 3,seriesNameCn: 3},
        {customSeriesCode: 4,seriesNameCn: 4},
        {customSeriesCode: 5,seriesNameCn: 5},
      ],
      search_parameter: {
        title: new String(),
        content: new String(),
        name: new String()
      },
      listDealerAsrDocument_parameter: new Object(),
    };
  },
  computed: {
  },
  methods: {
    search_click(){
      this.listDealerAsrDocument_parameter = new Object();
      if(this.search_parameter.title.length !== 0){
        this.listDealerAsrDocument_parameter.documentTitle = String(this.search_parameter.title);
      }
      if(this.search_parameter.content.length !== 0){
        this.listDealerAsrDocument_parameter.documentContent = String(this.search_parameter.content);
      }
      if(this.search_parameter.name.length !== 0){
        this.listDealerAsrDocument_parameter.createBy = String(this.search_parameter.name);
      }
      console.log('筛选',this.listDealerAsrDocument_parameter);
    },
    search_clear(){
      let string = new String();
      this.search_parameter.title = string;
      this.search_parameter.content = string;
      this.search_parameter.name = string;
      this.listDealerAsrDocument_parameter = new Object();
      console.log('重置',this.listDealerAsrDocument_parameter);
    },
    add(){
      this.add_revise.title = '添加';
      this.add_revise.visible = true;
      this.add_revise.type = 0;
    },
    handleOk(){
      console.log('handleOk');
      this.add_revise.form.validateFields((err, values)=>{
        if(!err){
          console.log('确定',values);
        }
      })
    },
    handleCancel(){
      console.log('handleCancel');
      this.add_revise.visible = false;
    },
    revise(data){
      this.add_revise.title = '修改';
      this.add_revise.visible = true;
      this.add_revise.type = 1;
      this.add_revise.parameter = data;
      this.$nextTick(()=>{
        this.add_revise.form.setFieldsValue({
          documentStatus: data.documentStatus,
          documentTitle: data.documentTitle,
          documentContent: data.documentContent,
          documentSeq: Number(data.documentSeq)
        });
      })
    },
    poer_delete(data){
      this.$confirm({
        title: '确认删除？',
        content: '删除后将无法恢复，是否删除？',
        onOk: ()=> {
          console.log('删除');
        },
        onCancel() {},
      });
    },
  },
  created() {
    setTimeout(()=>{
      for(let a = 0; a<14;a++){
        this.table_data.push({
          documentCategory: '类型'+a,
          documentTitle: '标题'+a,
          documentContent: '内容'+a,
          createByName: '创建人'+a,
          createTime: '2021-12-12 12:20:12',
          updateTime: '2021-12-12 12:20:12',
          documentSeq: '显示顺序'+a,
          documentStatus: '状态'+a
        })
      }
      this.table_loading = false;
    },3000)
    
  },
};
</script>

<style>
.opning_words{
  background-color: #f0f2f5;
  min-height: 100%;
}
.opning_words .search{
  background-color: #fff;
  padding: 24px 32px;
}
.tab-page-content{
  margin: 20px 20px 0;
}
</style>