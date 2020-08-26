在安卓手机上使用表单校验时，校验失败的问题：

原因是如果表单内部的输入框非输入类型，而是选择类型。在`van-form`表单默认是脱焦模式，即`validate-trigger="onBlur"`情况下，选择框不会有脱焦模式，在表单进行校验时，即便选择了响应的数据，也会走`failed`方法。所以会看到明明数据都有，但是还是会在`onFailed`中看到报错的日志。

修改代码如下，将表单校验触发时机修改为`validate-trigger="onChange" `。

当前情况只在安卓手机上会出现，web和ios上均不会出现

```vue
<van-form class="form-content" ref="myForm" validate-trigger="onChange" @submit="onSubmit" @failed="onFailed">
    <van-field
        v-else
        v-model="business.optionalBusinessName"
        name="business"
        label="出差公司"
        placeholder="请选择出差公司"
        readonly
        :rules="[{ required: companyList.length > 1 }]"
        :required="companyList.length > 1"
        :is-link="companyList.length > 1"
        @click="onClick('business', companyList.length > 1)"
    />
    <van-field
        v-model="costCenter.costCenterName"
        name="costCenter"
        :label="costCenterTitle"
        :placeholder="`请${costCenterIsManual ? '填写' : '选择'}${costCenterTitle}`"
        :readonly="!costCenterIsManual"
        :rules="[{ required: true }]"
        :required="costCenterIsManual || costCenterList.length > 1"
        :is-link="!costCenterIsManual && costCenterList.length > 1"
        @input="onInput"
        @click="onClick('costCenter', !costCenterIsManual && costCenterList.length > 1)"
    />
</van-form>
<PickerBottom
    :show="showPicker"
    :title="pickerTitle"
    :columns="columns"
    :value-key="valueKey"
    @confirm="onConfirm"
    @cancel="showPicker = false"
/>
```

