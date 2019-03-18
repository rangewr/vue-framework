<template>

    <div>

        <Row>
            <Col span="24">
                <div style="" class="doc-header">
                    <!--<h3 align="center">病例管理</h3>-->
                    <Form inline>
                        <!--<Form-item prop="user">-->

                        <!--</Form-item>-->
                        <Form-item>
                            <Button style="color:#fff; background-color: orange;border:1px solid orange"
                                    @click="inputQuery">查询全部
                            </Button>
                        </Form-item>
                        <Form-item style="margin-left: 0px">
                            <Button style="color:#fff; background-color: #19be6b;border:1px solid #19be6b"
                                    @click="selectUser()">条件查询
                            </Button>
                        </Form-item>
                        <Form-item>
                            <Button @click="handleAdd()" icon="plus-round">新增病例
                            </Button>
                        </Form-item>
                    </Form>

                    <Table align="center" :columns="columns" :data="data1">
                    </Table>

                    <Modal v-model="model1" title="查询" @on-ok="selOk" @on-cancel="selCancel">
                        <Form label-position="top">
                            <Form-item label="会员信息">
                                <Input style="width: 100%" v-model="input1" placeholder="会员编号 / 姓名">
                                </Input>
                            </Form-item>
                            <Form-item label="体检时间">
                                <div id="realRange" style="display: block;">
                                    <Date-picker type="daterange" :options="options2" placement="bottom-end"
                                                 placeholder="选择体检日期" style="width: 100%"
                                                 v-model="checkTime"></Date-picker>
                                </div>
                            </Form-item>
                        </Form>
                    </Modal>

                    <Modal v-model="model2" title="删除提示" @on-ok="delOk" @on-cancel="delCancel">
                        <h5 style="color: red;">删除后的数据不可恢复! ! !</h5><br>
                        <h6>是否确认删除 ?</h6>
                    </Modal>
                </div>

                <div class="u-pages" style="margin-bottom:20px; margin-top:10px;">
                    <ul style="display: inline;margin: 0px auto;">
                        <li v-if="showTail" class="crt"><a v-on:click="jumpTail(cur)">末页</a></li>
                        <li v-if="showTail" class="crt"><a v-on:click="plus(cur)">下一页</a></li>
                        <li v-bind:class='pageNo == cur?"crt":"nocrt"'><a @click="btnClick(pageNo)">{{pageNo}}</a></li>
                        <li v-if="showMoreTail" class="nocrt" style="line-height: 29px">...</li>
                        <li id="indexli" v-for="index in indexs" v-bind:class='index == cur?"crt":"nocrt"'
                            @click="btnClick(index)">
                            <a>{{index}}</a>
                        </li>
                        <li v-if="showPre" class="crt"><a v-on:click="minus(cur)"> 上一页 </a></li>
                        <li v-if="showPre" class="crt"><a v-on:click="jumpFirst(cur)"> 首页 </a></li>
                        <select v-model="pageSize" style="float: right;">
                            <option v-for="page  in pageSizeOption"
                                    :value="page">
                                {{page}}
                            </option>
                        </select>
                    </ul>
                </div>
            </Col>
        </Row>
    </div>
</template>
<script>
    import axios from "axios";
    import qs from "qs";
    import Cookies from "js-cookie";

    export default {
        data() {
            return {
                pageNo: 10,//页码总数
                cur: 1,//当前页码
                pageSize: 10,//每页显示行数
                showPre: true,//显示"上一页"和"首页"
                showTail: true,//显示"下一页"和"尾页"
                showMorePre: false,
                showMoreTail: false,
                indexs: [],
                onSel: false,
                pageSizeOption: [10, 15, 25, 50, 100, 200, 500],//页面显示行数备选
                delMark: 0,
                model1: false,
                model2: false,
                hospitalList: [],//医院列表
                columns: [
                    {
                        title: '病例编号',
                        // width: '60px',
                        key: 'Id'
                    },
                    {
                        title: '操作时间',
                        key: 'Update_time'
                    },
                    {
                        title: '体检单位',
                        key: 'Hospital_name'
                    },
                    {
                        title: '体检科室',
                        key: 'Hospital_department_name'
                    },
                    {
                        title: '会员编号',
                        key: 'Customer_id'
                    },
                    {
                        title: '会员姓名',
                        key: 'Customer_name'
                    },
                    {
                        title: '会员性别',
                        key: 'Customer_gender'
                    },
                    {
                        title: '会员年龄',
                        key: 'Customer_age'
                    },
                    {
                        title: '体检时间',
                        key: 'Cretate_time'
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
                                        type: 'success',
                                        size: 'small'
                                    },
                                    style: {
                                        marginRight: '5px'
                                    },
                                    on: {
                                        click: () => {
                                            this.viewDetail(params.index)
                                        }
                                    }
                                }, '详情'),
                                h('Button', {
                                    props: {
                                        type: 'error',
                                        size: 'small'
                                    },
                                    on: {
                                        click: () => {
                                            this.delCase(params.index)
                                        }
                                    }
                                }, '删除')
                            ]);
                        }
                    }
                ],//表单列设置
                data1: [],//表单数据
                formDynamic: {
                    items: [
                        {
                            value: 'xin'
                        }
                    ]
                },
                options2: {
                    disabledDate(date) {
                        const disabledDay = date.getDate();
                        return disabledDay === 15;
                    },
                    shortcuts: [
                        {
                            text: '今天',
                            value() {
                                const end = new Date();
                                const start = new Date();
                                return [start, end];
                            },
                        },
                        {
                            text: '昨天',
                            value() {
                                const end = new Date();
                                const start = new Date();
                                start.setTime(start.getTime() - 3600 * 1000 * 24 * 1);
                                end.setTime(end.getTime() - 3600 * 1000 * 24 * 1);
                                return [start, end];
                            },
                        },
                        {
                            text: '最近一周',
                            value() {
                                const end = new Date();
                                const start = new Date();
                                start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
                                return [start, end];
                            },
                        },
                        {
                            text: '最近一个月',
                            value() {
                                const end = new Date();
                                const start = new Date();
                                start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
                                return [start, end];
                            }
                        },
                        {
                            text: '最近三个月',
                            value() {
                                const end = new Date();
                                const start = new Date();
                                start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
                                return [start, end];
                            }
                        }
                    ]
                },//体检时间日历设置
                checkTime: [],//体检时间(高级查询)
                input1: "",//普通查询输入框
                input2: "",//选择医院
            }
        },//data
        created() {
            this.initHospital();
        },
        watch: {
            "pageSize": function toggle() {
                this.queryCaseInfo(this.cur);
            },
        },
        methods: {

            //跳转到首页
            jumpFirst(data) {
                var $this = this;
                data = 1;
                $this.cur = 1;
                if (data == 1) {
                    $this.showPre = false;
                } else {
                    $this.showPre = true;
                }
                $this.queryCaseInfo($this.cur);
                // $this.$am.ajax({
                //     url: window.$ApiConf.api_order_detail_list,
                //     type: 'GET',
                //     data: {start: 1},
                //     success: function (data) {
                //         console.log(data);
                //         $this.$set("records", data.record.records);
                //         $this.$set("start", data.record.query.start);
                //         $this.$set("total", data.record.query.total);
                //         $this.$set("limit", data.record.query.limit);
                //     }
                // })
                $this.showTail = true;
                // this.changePageNo();
                // return data;
            },

            //上一页
            minus(data) {
                var $this = this;
                data--;
                $this.cur = data;
                $this.showTail = true;
                if (data == 1) {//从第2页跳转到第1页,隐藏'首页'和'上一页'按钮
                    $this.showPre = false;
                } else {
                    $this.showPre = true;
                }
                this.queryCaseInfo($this.cur);
            },

            //下一页
            plus(data) {
                var $this = this;
                data++;
                $this.cur = data;
                $this.showPre = true;
                if (data == $this.pageNo) {
                    $this.showTail = false;
                } else {
                    $this.showTail = true;
                }
                this.queryCaseInfo($this.cur);
                return data;
            },

            //末页
            jumpTail(data) {
                var $this = this;
                data = $this.pageNo;
                $this.cur = data;
                if (data == $this.pageNo) {
                    $this.showTail = false;
                } else {
                    $this.showTail = true;
                }
                $this.showPre = true;
                this.queryCaseInfo($this.cur);
            },

            //点击页码跳转页面
            btnClick(data) {
                var $this = this;
                if (data == 1) {
                    $this.showPre = false;

                } else {
                    $this.showPre = true;
                }
                if (data == $this.pageNo) {
                    $this.showTail = false;
                    // this.toggleClass($this.pageNo);
                } else {
                    $this.showTail = true;
                }
                if (data != $this.cur) {//发送请求获取对应界面的数据
                    $this.cur = data;
                    this.queryCaseInfo($this.cur);
                }

            },

            //*********************分页开始******************************//

            //修改分页页码样式
            changePageNo() {
                var $this = this;
                $this.indexs = [];
                var ar = [];
                if ($this.cur * 1 > 3) {//大于3页时 显示123 或者234 或者345
                    ar.push($this.cur - 3);
                    ar.push($this.cur - 2);
                    ar.push($this.cur - 1);
                } else {//小于等于3页时   1 2 3 或12 或1
                    for (var i = 1; i < $this.cur * 1; i++) {
                        ar.push(i);
                    }
                }
                if ($this.cur * 1 != $this.pageNo * 1) {//1234 或者2345 或者3456
                    ar.push($this.cur * 1);
                }
                if ($this.cur * 1 < ($this.pageNo * 1 - 3)) {
                    ar.push($this.cur * 1 + 1);
                    ar.push($this.cur * 1 + 2);
                    ar.push($this.cur * 1 + 3);//1234+567或 2345+678 或者3456+789
                    if ($this.cur * 1 < ($this.pageNo * 1 - 4)) {//加上..
                        $this.showMoreTail = true;
                    } else {
                        $this.showMoreTail = false;
                    }
                } else {
                    $this.showMoreTail = false;
                    for (var i = ($this.cur * 1 + 1); i < $this.pageNo * 1; i++) {
                        ar.push(i);
                    }
                }
                this.indexs = ar.reverse().join("");
            },

            //查询该页码的数据
            queryCaseInfo(pageNo) {//根据页码查询数据
                this.cur = pageNo;
                if (this.checkTime.length > 0) {
                    var startTime = this.formatFunction(this.checkTime[0]);
                    var endTime = this.formatFunction(this.checkTime[1]);
                }
                this.queryCaseList(pageNo, this.input1, startTime, endTime);
            },

            //*********************分页结束******************************//

            //获取条件进行查询
            queryCaseList(pageNo, memInfo, startTime, endTime) {
                axios.get("/illness/GetCaseList", {
                    params: {
                        pageNo: pageNo,
                        memInfo: memInfo,
                        startTime: startTime,
                        endTime: endTime,
                        pageSize: this.pageSize,
                    }
                }).then(response => {
                    if (response.data.ErrCode == 0) {
                        const data0 = response.data;
                        this.data1 = data0.Result.caseInfos;
                        for (var i = 0; i < this.data1.length; i++) {
                            this.data1[i].Customer_gender = this.data1[i].Customer_gender == 1 ? "男" : "女";
                        }
                        if (data0.Result.ListCount % this.pageSize == 0) {//判断总页数
                            this.pageNo = parseInt(data0.Result.ListCount / this.pageSize)
                        } else {
                            this.pageNo = parseInt(data0.Result.ListCount / this.pageSize) + 1;
                        }
                        this.changePageNo();
                    } else {
                        this.$Message.error(response.data.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取病例信息异常");
                    console.log(error);
                });
            },

            //查看病例详情跳转界面
            viewDetail(index) {//修改用户信息时,对界面值初始化
                this.$router.push({
                    path: '/addCase', query: {
                        reportId: this.data1[index].Id,//病例编号
                        displayModel: true,//此字段用于判断是详情界面或者是新增界面
                    }
                });
            },

            //添加病例,跳转到添加界面
            handleAdd() {
                this.$router.push({
                    path: '/addCase', query: {
                        displayModel: false,//此字段用于判断是详情界面或者是新增界面,false表示新增界面
                    }
                });
            },

            //条件查询
            selectUser() {
                this.model1 = true;
            },

            //查询全部
            inputQuery() {
                this.checkTime = [];
                this.input1 = "";
                this.selOk();
            },

            //查询确定按钮
            selOk() {
                this.model1 = false;
                this.cur = 1;
                if (this.checkTime.length > 0) {
                    var startTime = this.formatFunction(this.checkTime[0]);
                    var endTime = this.formatFunction(this.checkTime[1]);
                }
                this.queryCaseList(1, this.input1, startTime, endTime);
            },

            //查询取消按钮
            selCancel() {
                this.model1 = false;
            },

            //删除病例提示
            delCase(index) {
                this.model2 = true;
                this.delMark = this.data1[index].Id;
            },

            //删除确定按钮
            delOk() {
                this.model2 = false;
                axios.post("/illness/DelCaseInfo", qs.stringify({//
                    reportId: this.delMark
                })).then(response => {
                    if (response.data.ErrCode == 0) {
                        this.$Message.success("删除成功");
                        this.selOk();
                    } else {
                        this.$Message.error(response.data.ErrMsg);
                    }
                }).catch(error => {
                    console.log("删除病例信息异常");
                    console.log(error);
                });
            },

            //删除取消按钮
            delCancel() {
                this.model2 = false;
            },

            initHospital() {
                axios.get("/illness/GetHospitalList", {}).then(response => {
                    const data0 = response.data;
                    if (data0.ErrCode == 0) {
                        this.hospitalList = data0.Result;
                        this.queryCaseInfo(1);
                    } else {
                        this.$Message.error(data0.ErrMsg);
                    }
                }).catch(error => {
                    console.log("获取机构异常");
                    console.log(error);
                })
            },

            formatFunction(time1) {//处理日期格式问题
                if (time1 == "") {
                    return ""
                }
                var dateee = time1.toJSON();
                var date1 = new Date(+new Date(dateee) + 8 * 3600 * 1000).toISOString().replace(/T/g, ' ').replace(/\.[\d]{3}Z/, '');
                var date2 = new Date(date1.replace(/-/, "/"));
                return date1;
            }
        },

    }
</script>

<style scoped>
    li {
        /*text-decoration: none; !*去掉前面的圆点*!*/
        list-style: none;
        float: right;
        margin-left: 3px;
        /*border: 1px solid #FFFFFF;*/
        /*background-color: #30DDEB;*/
    }

    li a {
        color: #040404;
        text-decoration: none;
        margin: 2px;
        display: block;
        line-height: 25px;
        text-align: center;
    }

    .crt {
        border: 1px solid #dcdcdc;
        background-color: #dcdcdc;
        line-height: 25px;
        border-radius: 4px;
        padding: 0px 5px;
    }

    .nocrt {
        border: 1px solid #dcdcdc;
        /*background-color: #dcdcdc;*/
        line-height: 25px;
        border-radius: 4px;
        padding: 0px 5px;
    }

    .u-pages {
        /*border: 1px solid red;*/
        /*margin: 0px auto;*/
    }

    select {
        width: 50px;
        padding: 4px 3px;
        color: #495060;
        border: 1px solid #dddee1;
        height: 32px;
        border-radius: 4px;
        font-size: 12px;
        transition: border .2s ease-in-out, background .2s ease-in-out, box-shadow .2s ease-in-out, -webkit-box-shadow .2s ease-in-out;
    }
</style>


