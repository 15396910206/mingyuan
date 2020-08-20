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
                <span class="font_14 marrt_20">消耗信息</span>
            </div>
            <div class="nav_header">
                <el-form :inline="true" class="demo-form-inline">
                    <el-form-item label="订单编号">
                        <el-input :value="billID" disabled size="mini" style="width: 200px;"></el-input>
                    </el-form-item>
                    <el-form-item label="经手单位" v-if="false">
                        <el-input :value="entityID" disabled size="mini"></el-input>
                    </el-form-item>
                    <el-form-item label="出入类型">
                        <el-input :value="typeValue" disabled size="mini"></el-input>
                    </el-form-item>
                    <el-form-item label="入库仓库">
                        <el-input :value="warehouseID" disabled size="mini"></el-input>
                    </el-form-item>
                    <el-form-item label="开单日期">
                        <el-date-picker size="mini" v-model="billDate" align="right" type="date"
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
                <span class="font_14 marrt_20">出库明细</span>
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
                    <el-table-column prop="inAmount" label="输入数量"></el-table-column>
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
                    <div class="con" style="display: flex;width: 50%">
                        <div style="display: inline-block;width:120px">出库类型：</div>
                        <div style="display: inline-block;">{{this.typeValue}}</div>
                    </div>
                </div>

                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div class="con" style="display: flex;width: 50%">
                        <div style="display: inline-block;width:120px">入库仓库：</div>
                        <div style="display: inline-block;">{{this.warehouseID}}</div>
                    </div>
                    <div style="display: inline-block;width: 50%">
                        <div style="display: inline-block;width:120px">开单时间：</div>
                        <div
                                style=" display: inline-block;"
                        >{{this.billDate}}
                        </div>
                    </div>
                </div>
                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div style="display: inline-block;width: 50%">
                        <div style="display: inline-block;width:120px">制单人：</div>
                        <div
                                style="display: inline-block;"
                        >{{this.accountName}}
                        </div>
                    </div>
                    <div style="display: inline-block;width: 50%">
                        <div style="display: inline-block;width:120px">备注：</div>
                        <div
                                style="display: inline-block;"
                        >{{this.remark}}
                        </div>
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
                <el-table-column prop="price" label="单价"></el-table-column>
                <el-table-column prop="amount" label="数量"></el-table-column>
                <el-table-column prop="totalPrice" label="金额"></el-table-column>
            </el-table>

            <div style="margin-top: 40px">
                <div
                        style="display: flex;justify-content: space-between;font-size: 15px;padding-top: 10px;padding-bottom: 10px;"
                >
                    <div style="display: inline-block;width: 25%">
                        <div style="display: inline-block;">制单/出库：</div>
                        <div style="display: inline-block;width: 120px;border-bottom: 1px solid black"></div>
                    </div>
                    <div style="display: inline-block;width: 25%">
                        <div style="display: inline-block;">审核：</div>
                        <div style="display: inline-block;width: 120px;border-bottom: 1px solid black"></div>
                    </div>
                    <div style="display: inline-block;width: 25%">
                        <div style="display: inline-block;">门店/仓库：</div>
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
                billID: "",
                typeValue: "",
                warehouseID: "", // 入库仓库
                entityID: "", // 经手单位
                billDate: "",
                verifyDate: "",
                remark: "",
                tableData: [],
                accountName: "",
            };
        },
        methods: {
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
            //单据打印
            printClick() {
                printJS({
                    printable: "printContent",
                    type: "html",
                    scanStyles: false,
                    header: "消耗出库单",
                    headerStyle: "text-align:center;font-size:18px",
                    style:
                        "table tr td,th { padding: 15px;text-align:center;}.el-table__header-wrapper{border-bottom:1px solid #000} .el-table__footer-wrapper{margin-top:25px;border-top:1px solid #000}",
                    // targetStyles: ["*"],
                });
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
                API.consumeOrderDetails(params)
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
        },
        mounted() {
            var that = this;
            var detail = that.$route.query.detail;
            that.billID = detail.billID;
            that.warehouseID = detail.warehouseName || "";
            that.entityID = detail.entityName || "";
            that.billDate = detail.billDate || "";
            that.verifyDate = detail.verifyDate || "";
            that.remark = detail.remark || "";
            that.typeValue = detail.stockIOTypeName;
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
