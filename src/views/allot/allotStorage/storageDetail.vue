<template>
    <div v-loading="loading">
        <div class="pad_15">
            <el-row>
                <el-col :span="24" class="text_right">
                    <el-button type="primary" size="mini" plain @click="printClick">打印单据</el-button>
                    <el-button type="primary" size="mini" plain @click="cancelClick">返回</el-button>
                    <el-button type="primary" size="mini" @click="affirmClick()" v-if="isState == '待收货'">确认收货
                    </el-button>
                </el-col>
            </el-row>
        </div>
        <div class="pad_5 backfff">
            <div class="pad_5 nav_header">
                <span class="font_14 marrt_20">调拨信息</span>
            </div>
            <div class="nav_header">
                <el-form :inline="true" class="demo-form-inline">
                    <el-form-item label="订单编号">
                        <el-input class="width_220" :value="billID" disabled size="mini"></el-input>
                    </el-form-item>
                    <el-form-item label="单据状态">
                        <el-input class="width_220" :value="isState" disabled size="mini"></el-input>
                    </el-form-item>
                    <el-form-item label="收货仓库">
                        <el-input class="width_220" :value="stockDestName" disabled size="mini"></el-input>
                    </el-form-item>
                    <el-form-item label="发货仓库">
                        <el-input class="width_220" :value="warehouseName" disabled size="mini"></el-input>
                    </el-form-item>
                    <el-form-item label="开单日期">
                        <el-date-picker size="mini" v-model="billDate" align="right" type="date"
                                        disabled></el-date-picker>
                    </el-form-item>
                    <el-form-item label="审核日期">
                        <el-date-picker size="mini" v-model="verifyDate" align="right" type="date"
                                        disabled></el-date-picker>
                    </el-form-item>
                    <el-form-item label="备注">
                        <el-input type="textarea" v-model="remark" disabled placeholder="请输入" size="mini"></el-input>
                    </el-form-item>
                </el-form>
            </div>
        </div>

        <div class="pad_10 backfff martp_15">
            <div class="pad_10 nav_header">
                <span class="font_14 marrt_20">调拨明细</span>
            </div>
            <div>
                <el-table
                        :data="tableData"
                        show-summary
                        :summary-method="getSummaries"
                        max-height="400"
                        style="width: 100%"
                >
                    <el-table-column type="index" label="序号"></el-table-column>
                    <el-table-column prop="productName" label="名称"></el-table-column>
                    <el-table-column prop="productID" label="货品编号"></el-table-column>
                    <el-table-column prop="Specification" label="规格"></el-table-column>
                    <el-table-column prop="inAmount" label="入库数量"></el-table-column>
                    <el-table-column prop="inUnit" label="选择单位"></el-table-column>
                    <el-table-column prop="maxPrice" label="选择单位单价（元）"></el-table-column>
                    <el-table-column prop="unit" label="最小单位"></el-table-column>
                    <el-table-column prop="price" label="最小单价（元）"></el-table-column>
                    <el-table-column prop="amount" label="最小单位数量合计"></el-table-column>
                    <el-table-column prop="totalPrice" label="合计金额（元）"></el-table-column>
                </el-table>
            </div>
        </div>
        <!--打印显示的东西-->
        <div class="backfff martp_15 none" id="printContent">
            <div class="pad_10">
                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div class="con" style="display: flex;width: 50%">
                        <div style="display: inline-block;width:120px">订单编号：</div>
                        <div style="display: inline-block;">{{this.billID}}</div>
                    </div>

                    <div style="display: inline-block;width: 50%">
                        <div style="display: inline-block;width:120px">单据状态：</div>
                        <div style=" display: inline-block;">{{this.isState}}</div>
                    </div>
                </div>

                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div class="con" style="display: inline-block;width: 50%">
                        <div style="display: inline-block;width:120px">发货仓库：</div>
                        <div style="display: inline-block;">{{this.stockDestName}}</div>
                    </div>
                    <div class="con" style="display: flex;width: 50%">
                        <div style="display: inline-block;width:120px">收货仓库：</div>
                        <div style="display: inline-block;">{{this.warehouseName}}</div>
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
                        <div style="display: inline-block;width:120px">开单日期：</div>
                        <div style="display: inline-block;">{{this.billDate}}</div>
                    </div>
                </div>
                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div style="display: inline-block;width: 100%">
                        <div style="display: inline-block;width:120px">备注：</div>
                        <div style="display: inline-block;">{{this.remark}}</div>
                    </div>
                </div>
            </div>
            <el-table
                    :data="tableData"
                    show-summary
                    :summary-method="getSummaries"
                    style="width: 100%;"
            >
                <el-table-column prop="productID" label="编号"></el-table-column>
                <el-table-column prop="productName" label="品名"></el-table-column>
                <el-table-column prop="inUnit" label="单位"></el-table-column>
                <el-table-column prop="Specification" label="规格"></el-table-column>
                <el-table-column prop="amount" label="数量"></el-table-column>
                <el-table-column prop="price" label="单价"></el-table-column>
                <el-table-column prop="totalPrice" label="金额"></el-table-column>
            </el-table>
            <div style="margin-top: 40px">
                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div class="con" style="display: inline-block;width: 20%">
                        <div style="display: inline-block;">制单/发货：</div>
                        <div style="display: inline-block;width: 120px;border-bottom: 1px solid black"></div>
                    </div>
                    <div class="con" style="display: inline-block;width: 20%">
                        <div style="display: inline-block;">送货：</div>
                        <div style="display: inline-block;width: 120px;border-bottom: 1px solid black"></div>
                    </div>
                    <div class="con" style="display: inline-block;width: 20%">
                        <div style="display: inline-block;">签收：</div>
                        <div style="display: inline-block;width: 120px;border-bottom: 1px solid black"></div>
                    </div>
                    <div class="con" style="display: inline-block;width: 20%">
                        <div style="display: inline-block;">财务：</div>
                        <div style="display: inline-block;width: 120px;border-bottom: 1px solid black"></div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import API from "@/api/allot";
    import printJS from "print-js";

    export default {
        data() {
            return {
                loading: false,
                billID: "", //订单编号
                isState: "", //单据状态
                stockDestName: "", // 收库仓库
                warehouseName: "", //发货仓库
                billDate: "",
                verifyDate: "",
                remark: "",
                tableData: [],
                accountName: "",
            };
        },
        methods: {
            // 单据状态
            formatStatus: function (row) {
                if (row.isState == 0) {
                    return "待收货";
                } else {
                    return "已完成";
                }
            },
            //单据打印
            printClick() {
                printJS({
                    printable: "printContent",
                    type: "html",
                    scanStyles: false,
                    header: "调拨入库单",
                    headerStyle: "text-align:center;font-size:18px",
                    style:
                        "table tr td,th { padding: 15px;text-align:center;}.el-table__header-wrapper{border-bottom:1px solid #000} .el-table__footer-wrapper{margin-top:25px;border-top:1px solid #000}",

                    // targetStyles: ["*"],
                });
            },
            getSummaries(param) {
                const {columns, data} = param;
                const sums = [];
                columns.forEach((column, index) => {
                    if (index === 0) {
                        sums[index] = "合计";
                        return;
                    }
                    if (
                        column.property == "productID" ||
                        column.property == "inAmount" ||
                        column.property == "maxPrice" ||
                        column.property == "price"
                    ) {
                        sums[index] = "";
                        return;
                    }
                    const values = data.map((item) => Number(item[column.property]));
                    if (!values.every((value) => isNaN(value))) {
                        sums[index] = values.reduce((prev, curr) => {
                            const value = Number(curr);
                            if (!isNaN(value)) {
                                return parseFloat((prev + curr).toFixed(2));
                            } else {
                                return prev;
                            }
                        }, 0);
                        if (column.property == "totalPrice") {
                            sums[index] = sums[index].toFixed(2);
                        }
                        // sums[index] += ' 元';
                    } else {
                        // sums[index] = 'N/A';
                    }
                });

                return sums;
            },
            // 取消
            cancelClick: function () {
                var that = this;
                that.$router.go(-1);
            },
            // 数据显示
            handleSearch: function () {
                var that = this;
                that.loading = true;
                var params = {
                    billID: that.billID,
                };
                API.getOrdersDetails(params)
                    .then((res) => {
                        if (res.stateCode == 100) {
                            res.data.forEach(function (item) {
                                item.totalPrice = (item.inAmount * item.maxPrice).toFixed(2);
                            });
                            that.tableData = res.data;
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
            // 确认收货
            affirmClick: function () {
                var that = this;
                that.loading = true;
                var params = {
                    billID: that.billID,
                };
                API.getOrdersConfirm(params)
                    .then((res) => {
                        if (res.stateCode == 100) {
                            that.$message({
                                message: "收货成功",
                            });
                            that.$router.go(-1);
                        } else {
                            that.$message.error({
                                message: res.message,
                                duration: 2000,
                            });
                        }
                    })
                    .catch(() => {
                        that.$message.error({
                            message: "网络连接失败，请稍后再试！",
                            duration: 2000,
                        });
                    })
                    .finally(function () {
                        that.loading = false;
                    });
            },
        },
        mounted() {
            var that = this;
            var detail = that.$route.query.detail;
            that.billID = detail.billID;
            that.warehouseName = detail.warehouseName || "";
            that.stockDestName = detail.stockDestName || "";
            that.billDate = detail.billDate || "";
            that.verifyDate = detail.verifyDate || "";
            that.isState = that.formatStatus(detail) || "";
            that.remark = detail.remark || "";
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
