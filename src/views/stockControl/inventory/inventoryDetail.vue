<template>
    <div v-loading="loading">
        <div class="pad_15">
            <el-row>
                <el-col :span="24" class="text_right">
                    <el-button type="primary" size="mini" plain @click="printClick">打印单据</el-button>
                    <el-button type="primary" size="mini" plain @click="cancelClick">返回</el-button>
                </el-col>
            </el-row>
        </div>
        <div class="pad_5 backfff">
            <div class="pad_5 nav_header">
                <span class="font_14 marrt_20">盘点信息</span>
            </div>
            <div class="nav_header">
                <el-form
                        :model="ruleForm"
                        :rules="rules"
                        ref="ruleForm"
                        :inline="true"
                        class="demo-form-inline"
                >
                    <el-form-item label="订单编号" prop="billID">
                        <el-input v-model="billID" size="mini" disabled class="width_220"></el-input>
                    </el-form-item>
                    <el-form-item label="盘点仓库" prop="warehouseID">
                        <el-select
                                v-model="ruleForm.warehouseID"
                                filterable
                                placeholder="请选择"
                                size="mini"
                                disabled
                        >
                            <el-option
                                    v-for="item in warehouseList"
                                    :key="item.warehouseID"
                                    :label="item.name"
                                    :value="item.warehouseID"
                            ></el-option>
                        </el-select>
                    </el-form-item>
                    <el-form-item label="盘点状态" prop="isStatus">
                        <el-input v-model="ruleForm.isStatus" size="mini" disabled></el-input>
                    </el-form-item>
                    <el-form-item label="盘点日期">
                        <el-date-picker
                                size="mini"
                                v-model="ruleForm.billDate"
                                align="right"
                                type="date"
                                disabled
                                placeholder="选择日期"
                        ></el-date-picker>
                    </el-form-item>
                    <!--<el-form-item label="审核日期">-->
                    <!--<el-date-picker-->
                    <!--size="mini"-->
                    <!--v-model="ruleForm.verifyDate"-->
                    <!--align="right"-->
                    <!--type="date"-->
                    <!--placeholder="选择日期"-->
                    <!--&gt;-->
                    <!--</el-date-picker>-->
                    <!--</el-form-item>-->
                    <el-form-item label="盘点人员">
                        <el-input v-model="ruleForm.empName" disabled size="mini"></el-input>
                    </el-form-item>
                    <el-form-item label="备注">
                        <el-input
                                disabled
                                type="textarea"
                                v-model="ruleForm.remark"
                                placeholder="请输入100字符以内文字"
                                maxlength="100"
                                show-word-limit
                                size="mini"
                        ></el-input>
                    </el-form-item>
                </el-form>
            </div>
        </div>

        <div class="pad_10 backfff martp_15">
            <div class="pad_10 nav_header">
                <el-row>
                    <el-col :span="20">
                        <span class="font_14 marrt_20">盘点明细</span>

                        <!--<el-button type="primary" size="mini" icon="el-icon-printer">打印</el-button>-->
                    </el-col>
                </el-row>
            </div>
            <div>
                <el-table
                        :data="replenishmentList"
                        ref="multipleTable"
                        tooltip-effect="dark"
                        max-height="400"
                        style="width: 100%"
                >
                    <el-table-column type="index" label="序号"></el-table-column>
                    <el-table-column prop="name" label="名称"></el-table-column>
                    <el-table-column prop="productID" label="货品编号"></el-table-column>
                    <el-table-column prop="Specification" label="规格"></el-table-column>
                    <el-table-column prop="Unit" label="默认单位"></el-table-column>
                    <el-table-column prop="oldBalance" label="盘点数量"></el-table-column>
                    <el-table-column prop="amount" label="库存数量"></el-table-column>
                    <el-table-column prop="plAmount" label="盈亏"></el-table-column>
                    <el-table-column prop="stockIOType" label="处理类型" :formatter="formatType"></el-table-column>
                    <el-table-column prop="remark" label="处理说明"></el-table-column>
                </el-table>
            </div>
        </div>

        <!--打印显示的东西-->
        <div class="martp_15 none" id="printContent">
            <div class="pad_10">
                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div class="con" style="display: flex;width: 50%">
                        <div style="display: inline-block;width:120px">订单编号：</div>
                        <div style="display: inline-block;">{{this.billID}}</div>
                    </div>
                    <div class="con" style="display: flex;width: 50%">
                        <div style="display: inline-block;width:120px">盘点仓库：</div>
                        <div style="display: inline-block;">{{warehouseName}}</div>
                    </div>
                </div>

                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div class="con" style="display: flex;width: 50%">
                        <div style="display: inline-block;width:120px">盘点状态：</div>
                        <div style="display: inline-block;">{{ruleForm.isStatus}}</div>
                    </div>
                    <div style="display: inline-block;width: 50%">
                        <div style="display: inline-block;width:120px">盘点日期：</div>
                        <div style=" display: inline-block;">{{ruleForm.billDate}}</div>
                    </div>
                </div>
                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div style="display: inline-block;width: 50%">
                        <div style="display: inline-block;width:120px">制单人：</div>
                        <div style="display: inline-block;">{{this.accountName}}</div>
                    </div>
                    <div style="display: inline-block;width: 50%">
                        <div style="display: inline-block;width:120px">备注：</div>
                        <div style="display: inline-block;">{{ruleForm.remark}}</div>
                    </div>
                </div>
            </div>
            <el-table :data="printList" style="width: 100%;">
                <el-table-column prop="productID" label="编号"></el-table-column>
                <el-table-column prop="name" label="品名"></el-table-column>
                <el-table-column prop="Unit" label="单位"></el-table-column>
                <el-table-column prop="Specification" label="规格"></el-table-column>
                <el-table-column prop="oldBalance" label="盘点数量"></el-table-column>
                <el-table-column prop="amount" label="库存数量"></el-table-column>
                <el-table-column prop="plAmount" label="盈亏"></el-table-column>
            </el-table>

            <div style="margin-top: 40px;">
                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div class="con" style="display: inline-block;width: 25%">
                        <div style="display: inline-block;">制单/盘点：</div>
                        <div style="display: inline-block;width: 120px;border-bottom: 1px solid black"></div>
                    </div>
                    <div class="con" style="display: inline-block;width: 25%">
                        <div style="display: inline-block;">门店/仓库：</div>
                        <div style="display: inline-block;width: 120px;border-bottom: 1px solid black"></div>
                    </div>
                    <div class="con" style="display: inline-block;width: 25%">
                        <div style="display: inline-block;">财务：</div>
                        <div style="display: inline-block;width: 120px;border-bottom: 1px solid black"></div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import API from "@/api/stockControl";
    import printJS from "print-js";

    export default {
        data() {
            return {
                loading: false,
                selectLoading: false,
                modalLoading: false,
                selectPage: 1,
                selectTotal: 0,
                selectName: "",
                tableData: [],
                billID: "",
                checked: "",
                productID: "",
                warehouseList: [], //仓库列表
                entityList: [], //经手单位
                productList: [], //产品列表
                barCodeList: [], //单位列表
                replenishmentList: [],
                multipleSelection: [],
                printList: [],
                accountName: "",
                modalTitle: "",
                dialogVisible: false,
                check: false,
                warehouseName: '',
                ruleForm: {
                    typeValue: "期初入库",
                    warehouseID: "", // 入库仓库
                    isStatus: "已完成",
                    billDate: new Date(),
                    verifyDate: new Date(),
                    remark: "",
                },
                rules: {
                    warehouseID: [
                        {required: true, message: "请选择入库仓库", trigger: "change"},
                    ],
                },
            };
        },
        methods: {
            formatState: function (row) {
                if (row.isStatus == 0) {
                    return "未处理";
                } else if (row.isStatus == 1) {
                    return "处理中 ";
                } else {
                    return "正常";
                }
            },
            formatType: function (row) {
                if (row.stockIOType == 0) {
                    return "平仓";
                } else if (row.stockIOType == 1) {
                    return "入库 ";
                } else {
                    return "出库";
                }
            },
            //单据打印
            printClick() {
                printJS({
                    printable: "printContent",
                    type: "html",
                    scanStyles: false,
                    header: "盘点盈亏单",
                    headerStyle: "text-align:center;font-size:18px",
                    style:
                        "table tr td,th { padding: 15px;text-align:center;}.el-table__header-wrapper{border-bottom:1px solid #000} .el-table__body-wrapper{padding-bottom:25px;border-bottom:1px solid #000}",
                    // targetStyles: ["*"],
                });
            },
            // 取消
            cancelClick: function () {
                var that = this;
                that.$router.go(-1);
            },
            // 仓库列表
            warehouseData: function () {
                var that = this;
                API.warehouseList().then((res) => {
                    if (res.stateCode == 100) {
                        that.warehouseList = res.data;
                    } else {
                        that.$message.error({
                            message: res.message,
                            duration: 2000,
                        });
                    }
                });
            },

            // 数据显示
            handleSearch: function () {
                let that = this;
                that.search();
            },
            // 数据显示
            search: function () {
                let that = this;
                that.loading = true;
                var params = {
                    billID: that.billID,
                };
                API.selectNumberByList(params)
                    .then((res) => {
                        if (res.stateCode == 100) {
                            res.data.list.forEach(p => {
                                if (p.stockIOType !== 0) {
                                    that.printList.push(p)
                                }
                            })
                            that.replenishmentList = res.data.list;

                        } else {
                            that.$message.error({
                                message: res.message,
                                duration: 2000,
                            });
                        }
                    })
                    .finally(function () {
                        that.loading = false;
                    });
            },
            priceDetailData: function (index) {
                var that = this;
                var params = {
                    productID: that.productID,
                };
                API.codeProductDetail(params).then((res) => {
                    if (res.stateCode == 100) {
                        that.replenishmentList[index].barCodeList = res.data.barCodeList;
                        that.replenishmentList[index].productID = that.productID;
                        that.replenishmentList[index].inAmount = 1;
                        that.replenishmentList[index].maxPrice = res.data.maxPrice;
                        that.replenishmentList[index].inUnit = res.data.inUnit;
                        that.replenishmentList[index].amount = res.data.amount;
                        that.replenishmentList[index].unitAmount = res.data.amount;
                        that.replenishmentList[index].unit = res.data.unit;
                        that.replenishmentList[index].price = res.data.price;
                        that.replenishmentList[index].totalPrice = (
                            res.data.amount * res.data.price
                        ).toFixed(2);
                    } else {
                        that.$message.error({
                            message: res.message,
                            duration: 2000,
                        });
                    }
                });
            },
            // 取消商品
            cancelProduct: function () {
                var that = this;
                that.replenishmentList.splice(0, 1);
            },
        },
        mounted() {
            var that = this;
            that.warehouseData();
            var detail = that.$route.query.detail;
            that.ruleForm.empName = detail.empIDName;
            that.billID = detail.billID;
            that.ruleForm.warehouseID = detail.warehouseID || "";
            that.ruleForm.billDate = detail.SysDate;
            that.ruleForm.remark = detail.remark;
            that.warehouseName = detail.warehouseName
            that.accountName = JSON.parse(localStorage.getItem("access-user")).empName;
            that.handleSearch();
        },
    };
</script>


<style scoped lang="scss">
    .none {
        width: 60%;
        position: absolute;
        top: -100000000000000px;
    }
</style>


