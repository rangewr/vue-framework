<template>
    <div>
        <!--病例信息填写-->
        <Row>
            <Col span="24">
                <!--机构/医师信息-->
                <div style="padding: 10px;border: 2px solid #777;border-radius:15px" class="doc-header">
                    <!--科室 主治医师 体检时间-->
                    <div class="doc-header" style="padding-left: 100px">
                        <Form ref="formInline" :model="uplineInfo" inline :label-width="80">
                            <Form-item label="体检单位">
                                <select v-model="uplineInfo.hospital">
                                    <option v-for="send  in hospital"
                                            :value="send.Id_hospital">
                                        {{send.Name}}
                                    </option>
                                </select>
                            </Form-item>
                            <span>&emsp;&emsp;&emsp;&emsp;&emsp;</span>
                            <Form-item label="体检科室">
                                <select v-model="uplineInfo.department">
                                    <option v-for="send  in department" name="sendSymbolId"
                                            :value="send.Id_hospital_department">
                                        {{send.Name}}
                                    </option>
                                </select>
                            </Form-item>
                            <span>&emsp;&emsp;&emsp;&emsp;&emsp;</span>
                            <Form-item prop="doctor" label="主治医师: ">
                                <select v-model="uplineInfo.doctor">
                                    <option v-for="send  in doctor" name="sendSymbolId"
                                            :value="send.Id_hospital_doctor">
                                        {{send.Name}}
                                    </option>
                                </select>
                            </Form-item>
                            <span>&emsp;&emsp;&emsp;&emsp;&emsp;</span>
                            <Form-item prop="createTime" label="检测时间: ">

                                <Date-picker type="date" v-model="uplineInfo.checkTime" :options="options1"
                                             placeholder="选择日期" style="width: 160px"></Date-picker>
                                <!--<Date-picker v-model="uplineInfo.checkTime" type="datetime" placeholder="选择日期和时间"-->
                                <!--style="width: 160px"></Date-picker>-->
                            </Form-item>
                        </Form>
                    </div>
                    <!--患者基本信息-->
                    <div class="divClass" style="">
                        <div style="margin-bottom: 10px" class="doc-content">
                            <h5 style="color: #9F79EE;">基本信息</h5>
                        </div>
                        <Form ref="formInline" :model="uplineInfo" inline :label-width="80">
                            <Form-item prop="department" label="会员账号:">
                                <Input v-model="uplineInfo.memNo">
                                </Input>
                            </Form-item>
                            <span>&emsp;&emsp;&emsp;&emsp;&emsp;</span>
                            <Form-item prop="doctor" label="姓名:">
                                <Input v-model="uplineInfo.userName">
                                </Input>
                            </Form-item>
                            <span>&emsp;&emsp;&emsp;&emsp;&emsp;</span>
                            <Form-item prop="createTime" label="性别:">
                                <Input v-model="uplineInfo.userGender">
                                </Input>
                            </Form-item>
                            <span>&emsp;&emsp;&emsp;&emsp;&emsp;</span>
                            <Form-item prop="createTime" label="年龄:">
                                <Input v-model="uplineInfo.userAge">
                                </Input>
                            </Form-item>
                        </Form>
                    </div>
                    <!--既往病史-->
                    <div class="divClass">
                        <div style="margin-bottom: 10px" class="doc-content">
                            <h5 style="color: #9F79EE;">既往病史</h5>
                        </div>
                        <div style="margin-right: 50px">
                            <Form>
                                <Form-item>
                                    <Input v-model="uplineInfo.historyInfo" type="textarea"
                                           :autosize="{minRows: 2,maxRows: 5}" placeholder="请如实填写曾患病信息..."></Input>
                                </Form-item>
                            </Form>
                        </div>
                    </div>
                    <!--检测项目-->
                    <div class="divClass">
                        <div style="margin-bottom: 10px" class="doc-content">
                            <h5 style="color: #9F79EE;">检测项目</h5>
                        </div>
                        <div style="margin-right: 50px">
                            <Button type="error" size="small" @click="clearAll()">清空</Button>&emsp;
                            <Button type="success" size="small" @click="AddNewItem()">新增</Button>
                            <br/><br/>
                            <Table ref="myTable" border :data="data1" :columns="columns"></Table>
                        </div>
                        <br/>
                    </div>
                    <!--诊断结论-->
                    <div class="divClass">
                        <div style="margin-bottom: 10px" class="doc-content">
                            <h5 style="color: #9F79EE;">诊断结论</h5>
                        </div>
                        <div style="margin-right: 50px">
                            <Form>
                                <Form-item>
                                    <Input v-model="uplineInfo.doctorResult" type="textarea"
                                           :autosize="{minRows: 3,maxRows: 3}" placeholder="根据体检结果，给出医疗方案..."></Input>
                                </Form-item>
                            </Form>
                        </div>
                    </div>
                    <br/>
                    <!--录入人 录入时间-->
                    <div>
                        <Row>
                            <Col span="14" style="border: 0px solid red;"><span>&emsp;</span></Col>
                            <Col span="10">
                                <Form ref="formInline" :model="uplineInfo" inline :label-width="80">
                                    <Form-item prop="department" label="录入人: ">
                                        <Input v-model="uplineInfo.createName" placeholder="录入人">
                                        </Input>
                                    </Form-item>
                                    <span>&emsp;&emsp;&emsp;&emsp;&emsp;</span>
                                    <Form-item prop="doctor" label="录入时间: ">
                                        <Input v-model="uplineInfo.createTime" readonly>
                                        </Input>
                                    </Form-item>
                                </Form>
                            </Col>
                        </Row>
                    </div>
                </div>
                <!--新增检查项目-->
                <Modal v-model="model1" title="新增项目" @on-ok="addOk" @on-cancel="addCancel">
                    <Form :model="formAdd" label-position="top">
                        <Form-item label="科目名称">
                            <select style="width: 100%;" v-model="formAdd.input1">
                                <option v-for="send  in subjects" name="sendSymbolId" :value="send.Id_physical_subject">
                                    {{send.Subject}}
                                </option>
                            </select>
                        </Form-item>
                        <Form-item label="项目名称">
                            <select style="width: 100%;" v-model="formAdd.input2">
                                <option v-for="send  in items" name="sendSymbolId"
                                        :value="send.Id_physical_subject_item">
                                    {{send.Name}}
                                </option>
                            </select>
                        </Form-item>
                        <Form-item label="检测结果">
                            <Input v-model="formAdd.input3"></Input>
                        </Form-item>
                        <Form-item label="参考值">
                            <Radio-group v-model="formItem.radio">
                                &emsp;<Radio label='r'>范围</Radio>&emsp;
                                <Radio label='v'>值</Radio>
                            </Radio-group>
                            <div id="realVal" style="display: none;">
                                <Input v-model="formAdd.input5"></Input>
                            </div>
                            <div id="realRange" style="display: block;">
                                <Input v-model="formAdd.input7" style="width: 46%;" placeholder="最小值"></Input>&emsp;- -&emsp;
                                <Input v-model="formAdd.input8" style="width: 46%;" placeholder="最大值"></Input>
                            </div>
                        </Form-item>
                        <Form-item label="单位">
                            <Input v-model="formAdd.input4"></Input>
                        </Form-item>
                        <Form-item label="检测医师">
                            <select style="width: 100%;" v-model="formAdd.input6">
                                <option v-for="send  in doctors" name="sendSymbolId" :value="send.Id_hospital_doctor">
                                    {{send.Name}}
                                </option>
                            </select>
                        </Form-item>
                    </Form>
                </Modal>
                <!--修改检查项目-->
                <Modal v-model="model2" title="修改项目" @on-ok="updateOk" @on-cancel="updateCancel">
                    <Form :model="formUpdate" label-position="top">
                        <Form-item label="科目名称">
                            <select style="width: 100%;" v-model="formUpdate.input1">
                                <option v-for="send  in subjects" name="sendSymbolId" :value="send.Id_physical_subject">
                                    {{send.Subject}}
                                </option>
                            </select>
                        </Form-item>
                        <Form-item label="项目名称">
                            <select style="width: 100%;" v-model="formUpdate.input2">
                                <option v-for="send  in items" name="sendSymbolId"
                                        :value="send.Id_physical_subject_item">
                                    {{send.Name}}
                                </option>
                            </select>
                        </Form-item>
                        <Form-item label="检测结果">
                            <Input v-model="formUpdate.input3"></Input>
                        </Form-item>
                        <Form-item label="参考值">
                            <Radio-group v-model="formItem.radioUpdate">
                                &emsp;<Radio label='r'>范围</Radio>&emsp;
                                <Radio label='v'>值</Radio>
                            </Radio-group>
                            <div id="realValUpdate" style="display: none;">
                                <Input v-model="formUpdate.input5"></Input>
                            </div>
                            <div id="realRangeUpdate" style="display: block;">
                                <Input v-model="formUpdate.input8" style="width: 46%;" placeholder="最小值"></Input>&emsp;-
                                -&emsp;
                                <Input v-model="formUpdate.input9" style="width: 46%;" placeholder="最大值"></Input>
                            </div>
                        </Form-item>
                        <Form-item label="单位">
                            <Input v-model="formUpdate.input4"></Input>
                        </Form-item>
                        <Form-item label="检测医师">
                            <select style="width: 100%;" v-model="formUpdate.input6">
                                <option v-for="send  in doctors" name="sendSymbolId" :value="send.Id_hospital_doctor">
                                    {{send.Name}}
                                </option>
                            </select>
                        </Form-item>
                        <input type="hidden" id="line">
                    </Form>
                </Modal>
                <!--删除检查项目提示-->
                <Modal v-model="model3" title="删除提示" @on-ok="delOk" @on-cancel="delCancel">
                    <p style="font-size: 16px">&emsp;&emsp;您将删除体检项目 <span
                            style="color: orangered;">[ {{itemName}} ]</span><br>&emsp;&emsp;是否确认删除?<br>
                    </p>
                </Modal>
            </Col>
        </Row>
        <!--提交按钮-->
        <Row>
            <Col span="24">
                <!--提交按钮-->
                <div style="width: 100%; text-align: center" class="doc-header">
                    <br>
                    <!--<input type="button" long @click="saveInfo()" value="保存提交">-->
                    <Button type="primary" @click="saveInfo()">保存提交</Button>
                    <br><br>
                </div>
            </Col>
        </Row>
    </div>

</template>
<script>
    import axios from "axios";
    import qs from "qs";

    export default {
        data() {
            return {
                formItem: {
                    radio: 'r',//新增时范围取值
                    radioUpdate: "r",//修改时范围取值
                },
                formAdd: {
                    input1: "",//科目名称
                    input2: "",//项目名称
                    input3: "",//检测结果
                    input4: "",//单位
                    input5: "",//正常值
                    input6: "",//检测医师
                    input7: "",//最小值
                    input8: "",//最大值
                },
                formUpdate: {
                    input1: "",//科目名称
                    input2: "",//项目名称
                    input3: "",//检测结果
                    input4: "",//单位
                    input5: "",//正常值
                    input6: "",//检测医师
                    input7: "",//修改时所在行
                    input8: "",//最小值
                    input9: "",//最大值
                },
                reportId: "",//病例id
                formDetail: [],//发送请求的表格数据
                model1: false,//新增项目
                model2: false,//修改项目
                model3: false,//删除提示
                hospital: [],//体检医院
                department: [],//机构下拉框
                doctor: [],//主治医师下拉框
                data1: [],//表格数据
                subjects: [],
                items: [],
                doctors: [],
                dataList: [],
                bool: true,//是否重复添加体检项目
                itemName: "",//删除前项目名称提示
                personId: "",//患者id
                columns: [{
                    title: '检测科目',
                    key: 'Subject',
                    align: 'center',
                }, {
                    title: '检测项目',
                    key: 'Items',
                    align: 'center',
                },
                    {
                        title: '检测结果',
                        align: 'center',
                        key: 'Result'
                    },
                    {
                        title: '单位',
                        align: 'center',
                        key: 'Unit'
                    },
                    {
                        title: '正常值范围',
                        align: 'center',
                        key: 'Normal_range'
                    },
                    {
                        title: '医师',
                        align: 'center',
                        key: 'Doctor'
                    },
                    {
                        title: '操作',
                        key: 'action',
                        width: 170,
                        align: 'center',
                        render: (h, params) => {
                            return h('div', [
                                h('Button', {
                                    props: {
                                        type: 'info',
                                        size: 'small'
                                    },
                                    style: {
                                        marginRight: '5px'
                                    },
                                    on: {
                                        click: () => {
                                            this.updateItem(params.index)
                                        }
                                    }
                                }, '修改'),
                                h('Button', {
                                    props: {
                                        backgroundColor: "orange",
                                        type: 'warning',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.delItem(params.index)
                                        }
                                    }
                                }, '删除')
                            ]);
                        }
                    }
                ],//表格列字段
                uplineInfo: {//文本框
                    hospital: "",//医院单位
                    department: "",//机构编码
                    doctor: "",//主治医师
                    checkTime: "",//检测时间
                    historyInfo: "",//既往病史
                    memNo: "",//会员号
                    userName: "",//姓名
                    userGender: "",//性别
                    userAge: "",//年龄
                    doctorResult: "",//诊断结论
                    createName: "",//录入人
                    createTime: "",//录入时间
                    initMark: true,//是否初始化标识
                },
                options1: {
                    disabledDate(date) {
                        return date && date.valueOf() > Date.now();
                    },
                },
                relationMark: false,
            }
        },

        created() {
            this.initHospital();
        },

        watch: {
            //监听会员账号输入框,当长度为10时,发送请求到后台查询会员信息
            "uplineInfo.memNo": function toggle() {
                if (this.uplineInfo.memNo.length == 10) {
                    if (this.uplineInfo.memNo.indexOf("YH") == -1) {
                        this.$Message.error("请输入合法会员号");
                    } else {
                        this.selectMemberInfo();
                    }
                }
            },
            //监听医院单位编码变化,连动科室下拉框
            "uplineInfo.hospital": function toggle() {
                if (this.uplineInfo.initMark) {
                    this.selectDepartment();
                }

            },
            //监听机构编码变化,变化时连动主治医师下拉框
            "uplineInfo.department": function toggle() {
                if (this.uplineInfo.initMark) {
                    this.selectDoctor();
                }
            },
            //监听新增时检测科目变化
            "formAdd.input1": function toggle() {
                if (this.formAdd.input1 != "") {
                    this.selectItemAdd(this.formAdd.input1);
                }
            },
            //监听新增时项目变化
            "formAdd.input2": function toggle() {
                if (this.formAdd.input1 != "") {
                    this.contactData();
                }
            },
            //监听修改时检测科目变化
            "formUpdate.input1": function toggle() {
                console.log("input1 and relationMark");
                console.log(this.formUpdate.input1);
                console.log(this.relationMark);
                if (this.formUpdate.input1 != "" && !this.relationMark) {
                    this.selectItemAdd(this.formUpdate.input1);
                }
                this.relationMark = false;
            },
            //监听修改时项目变化
            "formUpdate.input2": function toggle() {
                if (this.formUpdate.input1 != "") {
                    this.contactDataUpdate();
                }
            },
            //监听新增时取值范围单选框
            "formItem.radio": function toggle() {
                if (this.formItem.radio == "v") {
                    $("#realVal").css("display", "block");
                    $("#realRange").css("display", "none");
                } else {
                    $("#realVal").css("display", "none");
                    $("#realRange").css("display", "block");
                }
            },
            //监听新增时取值范围单选框
            "formItem.radioUpdate": function toggle() {
                if (this.formItem.radioUpdate == "v") {
                    $("#realValUpdate").css("display", "block");
                    $("#realRangeUpdate").css("display", "none");
                } else {
                    $("#realValUpdate").css("display", "none");
                    $("#realRangeUpdate").css("display", "block");
                }
            },
        },

        methods: {

            //监听新增时取值范围单选框
            contactData() {//对项目关联信息赋值
                if (this.formAdd.input2 != "") {
                    for (var i = 0; i < this.items.length; i++) {
                        if (this.formAdd.input2 * 1 == this.items[i].Id_physical_subject_item * 1) {
                            this.formAdd.input4 = this.items[i].Unit;
                            // this.formAdd.input5 = this.items[i].Normal_range;
                        }
                    }
                } else {
                    this.formAdd.input4 = "";
                    this.formAdd.input5 = "";
                }
            },

            //初始化体检单位(医院)
            initHospital() {
                axios.get("/illness/GetHospitalList", {}).then(response => {
                    const data0 = response.data;
                    if (data0.ErrCode == 0) {
                        this.hospital = data0.Result;
                    } else {
                        this.$Message.error(data0.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取机构异常");
                    console.log(error);
                })

                //判断是新增界面还是详情界面,详情界面时查询订单
                var displayModel = this.$route.query.displayModel;
                var reportId = this.$route.query.reportId;
                if (displayModel || displayModel == 'true') {//true表示详情界面
                    // this.selectDepartment();
                    this.uplineInfo.initMark = false;
                    this.queryCaseDetail(reportId);
                    this.reportId = reportId;
                } else {
                    //初始化当前录入时间
                    var date = new Date();
                    var year = date.getFullYear(); //获取当前年份
                    var mon = date.getMonth() + 1; //获取当前月份
                    var da = date.getDate(); //获取当前日
                    var h = date.getHours(); //获取小时
                    var m = date.getMinutes(); //获取分钟
                    var s = date.getSeconds(); //获取秒
                    var time = year + "-" + mon + "-" + da + " " + h + ":" + m + ":" + s;
                    this.uplineInfo.createTime = time;
                }

            },

            //根据医院查询科室
            selectDepartment() {
                axios.get("/illness/GetDepartmentList", {
                    params: {
                        hospitalId: this.uplineInfo.hospital
                    }
                }).then(response => {
                    const data0 = response.data;
                    if (data0.ErrCode == 0) {
                        this.department = data0.Result;
                    } else {
                        this.$Message.error(data0.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取机构异常");
                    console.log(error);
                })
            },

            //根据科室查询主治医师
            selectDoctor() {
                axios.get("/illness/GetDoctorList", {
                    params: {
                        hospitalId: this.uplineInfo.hospital,
                        departmentNo: this.uplineInfo.department
                    }
                }).then(response => {
                    if (response.data.ErrCode == 0) {
                        const data0 = response.data;
                        this.doctor = data0.Result;
                    } else {
                        this.$Message.error(response.data.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取会员信息异常");
                    console.log(error);
                });
            },

            //修改体检项目按钮
            updateItem(index) {
                this.model2 = true;
                this.relationMark = true;
                $("#line").val(index);
                for (var i = 0; i < this.subjects.length; i++) {
                    if (this.data1[index].Subject == this.subjects[i].Subject) {
                        this.formUpdate.input1 = this.subjects[i].Id_physical_subject;
                        break;
                    }
                }
                for (var i = 0; i < this.items.length; i++) {
                    if (this.data1[index].Items == this.items[i].Name) {
                        this.formUpdate.input2 = this.items[i].Id_physical_subject_item;
                        break;
                    }
                }
                console.log(this.doctors);
                for (var i = 0; i < this.doctors.length; i++) {
                    if (this.data1[index].Doctor == this.doctors[i].Name) {
                        this.formUpdate.input6 = this.doctors[i].Id_hospital_doctor;
                        break;
                    }
                }
                this.formUpdate.input3 = this.data1[index].Result;
                this.formUpdate.input4 = this.data1[index].Unit;
                if (this.data1[index].Normal_range.indexOf("-") != -1) {
                    var normalRange = this.data1[index].Normal_range.split("-");
                    this.formUpdate.input8 = normalRange[0];
                    this.formUpdate.input9 = normalRange[1];
                    this.formItem.radioUpdate = "r";
                } else {
                    this.formUpdate.input5 = this.data1[index].Normal_range;
                    this.formItem.radioUpdate = "v";
                }
            },

            //删除体检项目
            delItem(index) {
                this.model3 = true;
                this.itemName = this.data1[index].Subject;
                $("#line").val(index);
            },

            //对项目关联信息赋值
            contactDataUpdate() {
                if (this.formUpdate.input2 != "") {
                    for (var i = 0; i < this.items.length; i++) {
                        if (this.formUpdate.input2 * 1 == this.items[i].Id_physical_subject_item * 1) {
                            this.formUpdate.input4 = this.items[i].Unit;
                            // this.formUpdate.input5 = this.items[i].Normal_range;
                        }
                    }
                } else {
                    this.formUpdate.input4 = "";
                    this.formUpdate.input5 = "";
                }
            },

            //根据科目查询对应的项目/医师集合
            selectItemAdd(subNo) {
                axios.get("/illness/GetItemList", {
                    params: {
                        subNo: subNo
                    }
                }).then(response => {
                    if (response.data.ErrCode == 0) {
                        const data0 = response.data;
                        this.items = data0.Result;
                        this.selectDoctorAdd(subNo);//查询对应的医师
                    } else {
                        this.$Message.error(response.data.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取会员信息异常");
                    console.log(error);
                });
            },

            //新增体检科目时根据项目查询医师
            selectDoctorAdd(subNo) {
                axios.get("/illness/GetDoctorList", {
                    params: {
                        departmentNo: subNo
                    }
                }).then(response => {
                    if (response.data.ErrCode == 0) {
                        const data0 = response.data;
                        this.doctors = data0.Result;
                    } else {
                        this.$Message.error(response.data.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取会员信息异常");
                    console.log(error);
                });
            },

            //查询患者信息
            selectMemberInfo() {
                axios.get("/illness/GetMemberInfo", {
                    params: {
                        memNo: this.uplineInfo.memNo
                    }
                }).then(response => {
                    if (response.data.ErrCode == 0) {
                        const data0 = response.data;
                        if (data0.Result != null) {
                            this.uplineInfo.userName = data0.Result.Name;
                            this.uplineInfo.userAge = data0.Result.Age;
                            this.uplineInfo.userGender = data0.Result.Gender == "1" ? "男" : "女";
                            this.personId = data0.Result.Id_physical_person;
                        } else {
                            this.$Message.error("未查询到会员信息");
                        }
                    } else {
                        this.$Message.error(response.data.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取会员信息异常");
                    console.log(error);
                });
            },

            //新增按钮点击函数
            AddNewItem() {
                axios.get("/illness/GetSubjectList", {}).then(response => {
                    const data0 = response.data;
                    if (data0.ErrCode == 0) {
                        this.subjects = data0.Result;
                        this.model1 = true;
                    } else {
                        this.$Message.error(data0.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取机构异常");
                    console.log(error);
                })
            },

            //清空已填写的体检项目
            clearAll() {
                this.data1.length = 0;
                this.data1.push();
            },

            //新增弹框确定按钮,确定后验证数据合法,存储到数据库,保存成功后弹框上数据清空
            addOk() {
                var formObject = {
                    Subject: this.formAdd.input1,
                    Items: this.formAdd.input2,
                    Result: this.formAdd.input3,
                    Unit: this.formAdd.input4,
                    Normal_range: this.formItem.radio == "r" ? this.formAdd.input7 + "-" + this.formAdd.input8 : this.formAdd.input5,
                    Doctor: this.formAdd.input6
                };
                var errInfo = this.checkNotNull(formObject);
                if (errInfo != "") {
                    this.$Message.error(errInfo);
                } else if (this.formAdd.input8 * 1 < this.formAdd.input7 * 1) {
                    this.$Message.error("取值范围有误");
                } else {
                    // this.formDetail.push(formObject);
                    for (var i = 0; i < this.subjects.length; i++) {
                        if (formObject.Subject == this.subjects[i].Id_physical_subject) {
                            formObject.Subject = this.subjects[i].Subject;
                            break;
                        }
                    }
                    for (var i = 0; i < this.items.length; i++) {
                        if (formObject.Items == this.items[i].Id_physical_subject_item) {
                            formObject.Items = this.items[i].Name;
                            break;
                        }
                    }
                    for (var i = 0; i < this.doctors.length; i++) {
                        if (formObject.Doctor == this.doctors[i].Id_hospital_doctor) {
                            formObject.Doctor = this.doctors[i].Name;
                            break;
                        }
                    }
                    //验证体检项目是否已添加过
                    for (var i = 0; i < this.data1.length; i++) {
                        if (this.data1[i].Subject == formObject.Subject && this.data1[i].Items == formObject.Items) {
                            this.$Message.error("此项目在第 " + (i + 1) + " 行已添加!");
                            this.bool = false;
                        }
                    }
                    if (this.bool) {
                        this.data1.push(formObject);
                    }
                    this.bool = true;
                    this.formAdd.input1 = "";
                    this.formAdd.input2 = "";
                    this.formAdd.input3 = "";
                    this.formAdd.input4 = "";
                    this.formAdd.input5 = "";
                    this.formAdd.input6 = "";
                    this.formAdd.input7 = "";
                    this.formAdd.input8 = "";
                    this.model1 = false;
                }
            },

            //新增时取消,取消时填写的数据不清空
            addCancel() {
                this.model1 = false;
            },

            //修改确定按钮
            updateOk() {
                var formObject = {
                    Subject: this.formUpdate.input1,
                    Items: this.formUpdate.input2,
                    Result: this.formUpdate.input3,
                    Unit: this.formUpdate.input4,
                    Normal_range: this.formItem.radioUpdate == "r" ? this.formUpdate.input8 + "-" + this.formUpdate.input9 : this.formUpdate.input5,
                    Doctor: this.formUpdate.input6
                };
                var errInfo = this.checkNotNull(formObject);
                if (errInfo != "") {
                    this.$Message.error(errInfo);
                } else if (this.formUpdate.input9 * 1 < this.formUpdate.input8 * 1) {
                    this.$Message.error("取值范围有误");
                } else {
                    var tempIndex = $("#line").val() * 1;
                    // this.formDetail[tempIndex] = formObject;
                    for (var i = 0; i < this.subjects.length; i++) {
                        if (formObject.Subject == this.subjects[i].Id_physical_subject) {
                            formObject.Subject = this.subjects[i].Subject;
                            break;
                        }
                    }
                    for (var i = 0; i < this.items.length; i++) {
                        if (formObject.Items == this.items[i].Id_physical_subject_item) {
                            formObject.Items = this.items[i].Name;
                            break;
                        }
                    }
                    for (var i = 0; i < this.doctors.length; i++) {
                        if (formObject.Doctor == this.doctors[i].Id_hospital_doctor) {
                            formObject.Doctor = this.doctors[i].Name;
                            break;
                        }
                    }
                    this.data1[tempIndex] = formObject;
                    this.data1.push();
                    // this.model1 = false;
                }
                this.model2 = false;
                this.relationMark = false;
                this.selectItemAdd();
            },

            //修改取消按钮
            updateCancel() {
                this.model2 = false;
                this.relationMark = false;
                this.selectItemAdd();
            },

            //确认删除
            delOk() {
                var index = $("#line").val();
                this.data1.splice(index, 1);
                this.model3 = false;
            },

            //取消删除
            delCancel() {
                this.model3 = false;
            },

            //保存病例信息
            saveInfo() {
                var displayModel = this.$route.query.displayModel;
                if (displayModel || displayModel == "true") {//displayModel为true时表示详情界面
                    this.updateInfo();//调用修改信息方法
                } else {
                    this.submitInfo();
                }
            },

            //添加体检报告
            submitInfo() {
                var allInfo = {
                    department: this.uplineInfo.department,  //部门
                    doctor: this.uplineInfo.doctor,          //主治医师
                    checkTime: this.uplineInfo.checkTime,//检查时间
                    historyInfo: this.uplineInfo.historyInfo,//既往病史
                    memNo: this.uplineInfo.memNo,//患者编号
                    userName: this.uplineInfo.userName,//患者姓名
                    userGender: this.uplineInfo.userGender,//患者性别
                    userAge: this.userAge,//患者年龄
                    checkItems: this.data1,//体检科目
                    doctorResult: this.uplineInfo.doctorResult,//诊断结果
                    createName: this.uplineInfo.createName,//录入人
                    createTime: this.uplineInfo.createTime,//录入时间
                }
                var msg = this.checkInfoNotNull(allInfo);
                if (msg != "") {
                    this.$Message.error(msg);
                } else {
                    const formDetail = this.data1;
                    for (var index = 0; index < formDetail.length; index++) {
                        for (var i = 0; i < this.subjects.length; i++) {
                            if (formDetail[index].Subject == this.subjects[i].Subject) {
                                formDetail[index].Subject = this.subjects[i].Id_physical_subject;
                                break;
                            }
                        }
                        for (var i = 0; i < this.items.length; i++) {
                            if (formDetail[index].Items == this.items[i].Name) {
                                formDetail[index].Items = this.items[i].Id_physical_subject_item;
                                break;
                            }
                        }
                        for (var i = 0; i < this.doctors.length; i++) {
                            if (formDetail[index].Doctor == this.doctors[i].Name) {
                                formDetail[index].Doctor = this.doctors[i].Id_hospital_doctor;
                                break;
                            }
                        }
                    }
                    axios.post("/illness/AddCaseInfo", qs.stringify({//等待go修正   vue pass
                        department: this.uplineInfo.department,  //部门
                        doctor: this.uplineInfo.doctor,          //主治医师
                        checkTime: this.formatFunction(this.uplineInfo.checkTime),//检查时间
                        historyInfo: this.uplineInfo.historyInfo,//既往病史
                        memNo: this.personId,//患者编号
                        userName: this.uplineInfo.userName,//患者姓名
                        userGender: this.uplineInfo.userGender == "男" ? 1 : 2,//患者性别
                        userAge: this.uplineInfo.userAge,//患者年龄
                        checkItems: JSON.stringify(formDetail),//体检科目
                        doctorResult: this.uplineInfo.doctorResult,//诊断结果
                        createName: this.uplineInfo.createName,//录入人
                        createTime: this.formatFunction(this.uplineInfo.createTime),//录入时间
                    })).then(response => {
                        if (response.data.ErrCode == 0) {
                            this.$Message.success("保存成功");
                            this.$router.push({
                                path: '/medical', query: {}
                            });
                        } else {
                            this.$Message.error(response.data.ErrMsg);
                        }
                    }).catch(error => {
                        console.log("更新会议信息异常");
                        console.log(error);
                    });
                }
            },

            //修改病例详情提交保存
            updateInfo() {
                var allInfo = {
                    department: this.uplineInfo.department,  //部门
                    doctor: this.uplineInfo.doctor,          //主治医师
                    checkTime: this.uplineInfo.checkTime,//检查时间
                    historyInfo: this.uplineInfo.historyInfo,//既往病史
                    memNo: this.uplineInfo.memNo,//患者编号
                    userName: this.uplineInfo.userName,//患者姓名
                    userGender: this.uplineInfo.userGender,//患者性别
                    userAge: this.userAge,//患者年龄
                    checkItems: this.data1,//体检科目
                    doctorResult: this.uplineInfo.doctorResult,//诊断结果
                    createName: this.uplineInfo.createName,//录入人
                    createTime: this.uplineInfo.createTime,//录入时间
                }
                var msg = this.checkInfoNotNull(allInfo);
                if (msg != "") {
                    this.$Message.error(msg);
                } else {
                    const formDetail = this.data1;
                    for (var index = 0; index < formDetail.length; index++) {
                        for (var i = 0; i < this.subjects.length; i++) {
                            if (formDetail[index].Subject == this.subjects[i].Subject) {
                                formDetail[index].Subject = this.subjects[i].Id_physical_subject;
                                break;
                            }
                        }
                        for (var i = 0; i < this.items.length; i++) {
                            if (formDetail[index].Items == this.items[i].Name) {
                                formDetail[index].Items = this.items[i].Id_physical_subject_item;
                                break;
                            }
                        }
                        for (var i = 0; i < this.doctors.length; i++) {
                            if (formDetail[index].Doctor == this.doctors[i].Name) {
                                formDetail[index].Doctor = this.doctors[i].Id_hospital_doctor;
                                break;
                            }
                        }
                    }
                    axios.post("/illness/UpdateCaseInfo", qs.stringify({//等待go修正   vue pass
                        reportId: this.reportId,//病例id
                        department: this.uplineInfo.department,  //部门
                        doctor: this.uplineInfo.doctor,          //主治医师
                        checkTime: this.formatFunction(this.uplineInfo.checkTime),//检查时间
                        historyInfo: this.uplineInfo.historyInfo,//既往病史
                        memNo: this.personId,//患者编号
                        userName: this.uplineInfo.userName,//患者姓名
                        userGender: this.uplineInfo.userGender == "男" ? 1 : 2,//患者性别
                        userAge: this.uplineInfo.userAge,//患者年龄
                        checkItems: JSON.stringify(formDetail),//体检科目
                        doctorResult: this.uplineInfo.doctorResult,//诊断结果
                        createName: this.uplineInfo.createName,//录入人
                        createTime: this.formatFunction(this.uplineInfo.createTime),//录入时间
                    })).then(response => {
                        if (response.data.ErrCode == 0) {
                            this.$Message.success("保存成功");
                            this.$router.push({
                                path: '/medical', query: {}
                            });
                        } else {
                            this.$Message.error(response.data.ErrMsg);
                        }
                    }).catch(error => {
                        console.log("更新会议信息异常");
                        console.log(error);
                    });
                }
            },

            //详情界面查询订单
            queryCaseDetail(reportId) {
                //查询订单信息
                axios.get("/illness/GetCaseInfo", {
                    params: {
                        reportId: reportId
                    }
                }).then(response0 => {
                    const data0 = response0.data;
                    if (data0.ErrCode == 0) {
                        this.uplineInfo.hospital = data0.Result.hospital;
                        //根据医院查询科室
                        axios.get("/illness/GetDepartmentList", {
                            params: {
                                hospitalId: this.uplineInfo.hospital
                            }
                        }).then(response1 => {
                            const data01 = response1.data;
                            if (data01.ErrCode == 0) {
                                this.department = data01.Result;
                                this.uplineInfo.department = data0.Result.department;
                                //根据科室查询主治医师
                                axios.get("/illness/GetDoctorList", {
                                    params: {
                                        hospitalId: this.uplineInfo.hospital,
                                        departmentNo: this.uplineInfo.department
                                    }
                                }).then(response2 => {
                                    if (response2.data.ErrCode == 0) {
                                        const data02 = response2.data;
                                        this.doctor = data02.Result;
                                        this.uplineInfo.doctor = data0.Result.doctor;
                                        this.uplineInfo.initMark = true;
                                        this.queryItemDetial(reportId);
                                    } else {
                                        this.$Message.error(response.data.ErrMsg);
                                    }
                                }).catch(error => {
                                    console.log("获取主治医师信息异常");
                                    console.log(error);
                                });
                            } else {
                                this.$Message.error(data01.ErrMsg);
                            }
                        }).catch(error => {
                            console.log("获取机构异常");
                            console.log(error);
                        })
                        this.uplineInfo.checkTime = data0.Result.checkTime;
                        this.uplineInfo.historyInfo = data0.Result.historyInfo;
                        this.uplineInfo.memNo = data0.Result.memNo;
                        //会员的姓名,年龄,性别三个字段赋值必须保留,在新增是可能会修改会员的信息,
                        //此时造成会员信息与信息表中的会员信息不一致,不能使用会员号查询信息
                        this.uplineInfo.userName = data0.Result.userName;//姓名
                        this.uplineInfo.userGender = data0.Result.userGender;//性别
                        this.uplineInfo.userAge = data0.Result.userAge;//年龄
                        this.uplineInfo.doctorResult = data0.Result.doctorResult;//诊断结果
                        this.uplineInfo.createName = data0.Result.createName;//录入人
                        this.uplineInfo.createTime = data0.Result.createTime;//创建时间
                    } else {
                        this.$Message.error(data0.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取机构异常");
                    console.log(error);
                })
            },

            //详情界面查询检测项目
            queryItemDetial(reportId) {
                axios.get("/illness/GetItemsInfo", {
                    params: {
                        reportId: reportId
                    }
                }).then(response1 => {
                    const data0 = response1.data;
                    if (data0.ErrCode == 0) {
                        this.data1 = data0.Result;//data数据在后台以map形式组装好,直接对表格数据赋值即可
                        //初始化科目
                        axios.get("/illness/GetSubjectList", {}).then(response2 => {
                            const data0 = response2.data;
                            if (data0.ErrCode == 0) {
                                this.subjects = data0.Result;
                                this.selectItemAdd();//ojbk
                            } else {
                                this.$Message.error(data0.ErrMsg);
                            }
                        }).catch(error => {
                            console.log("获取机构异常");
                            console.log(error);
                        })
                    } else {
                        this.$Message.error(data0.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取机构异常");
                    console.log(error);
                })
            },

            //处理日期格式问题
            formatFunction(time1) {
                if (typeof (time1) != "string") {
                    var dateee = time1.toJSON();
                    var date1 = new Date(+new Date(dateee) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '');
                    return date1;
                } else {
                    return time1;
                }
            },

            //检测页面数据是否全部填写正确,是否为空
            checkInfoNotNull(allInfo) {
                if (allInfo.department == "") return "科室名称不能为空";
                if (allInfo.doctor == "") return "主治医师不能为空";
                if (allInfo.checkTime == "") return "检查时间不能为空";
                if (allInfo.historyInfo == "") return "既往病史不能为空";
                if (allInfo.memNo == "") return "患者编号不能为空";
                if (allInfo.userName == "" || allInfo.userGender == "" || allInfo.userAge == "") return "未查询到患者信息";
                if (allInfo.checkItems.length == 0) return "体检科目不能为空";
                if (allInfo.doctorResult == "") return "诊断结果不能为空";
                if (allInfo.createName == "") return "录入人不能为空";
                if (allInfo.createTime == "") return "录入时间不能为空";
                return "";
            },

            //检测值不为空
            checkNotNull(formObject) {
                if (formObject.Subject == "") return "科目不能为空";
                if (formObject.Items == "") return "项目不能为空";
                if (formObject.Result == "") return "检测结果不能为空";
                if (formObject.Doctor == "") return "检测医师不能为空";
                return "";
            },
        },
    }
</script>
<!-- scoped用于解决界面加载后样式不加载问题，但是刷新后可以恢复正常，scoped可以解决 -->
<style scoped>

    select {
        width: 162px;
        padding: 4px 3px;
        color: #495060;
        border: 1px solid #dddee1;
        height: 32px;
        border-radius: 4px;
        font-size: 12px;
        transition: border .2s ease-in-out, background .2s ease-in-out, box-shadow .2s ease-in-out, -webkit-box-shadow .2s ease-in-out;
    }

    .divClass {
        /*background-color: #FCFCFC;*/
        border: 1px solid #888;
        padding-left: 100px;
    }

    input[type='button'] {
        margin: 10px auto;
        width: 200px;
        height: 30px;
        background-color: #0e83cd;
        color: white;
        border: 1px solid #0e83cd;
        border-radius: 4px;
    }

</style>


