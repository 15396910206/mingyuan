<template>
    <div v-loading="loading">
        <div class="pad_10_15">
            <el-row>
                <el-col :span="24" class="text_right">
                    <el-button type="primary" size="mini" plain @click="cancelClick">返回</el-button>
                    <el-button type="primary" size="mini"  @click="cancellationClick"  v-loading="modalLoading">单据作废
                    </el-button>
                    <el-button type="primary" size="mini" plain v-loading="modalLoading" @click="saveClick">保存修改</el-button>
                    <el-button type="primary" size="mini" v-loading="modalLoading" @click="affirmClick">确认审核</el-button>
                </el-col>
            </el-row>
        </div>
        <div class="pad_5 backfff">
            <div class="pad_5 nav_header">
                <span class="font_14 marrt_20">调拨信息</span>
            </div>
            <div class="nav_header">
                <el-form :model="ruleForm" :rules="rules" ref="ruleForm" :inline="true" class="demo-form-inline">
                    <el-form-item label="订单编号">
                        <el-input :value="billID" disabled size="mini" class="width_220"></el-input>
                    </el-form-item>
                    <el-form-item label="单据状态">
                        <el-input :value="isConfirm" disabled size="mini"></el-input>
                    </el-form-item>
                    <el-form-item label="发货仓库" prop="srcWarehouseID" @change="warehouseChange">
                        <el-select v-model="ruleForm.srcWarehouseID" filterable disabled placeholder="请选择" size="mini">
                            <el-option
                                    v-for="item in warehouseList"
                                    :key="item.warehouseID"
                                    :label="item.name"
                                    :value="item.warehouseID">
                            </el-option>
                        </el-select>
                    </el-form-item>

                    <el-form-item label="收货仓库" prop="warehouseID">
                        <el-select v-model="ruleForm.warehouseID" filterable disabled placeholder="请选择" size="mini">
                            <el-option
                                    v-for="item in warehouseList"
                                    :key="item.warehouseID"
                                    :label="item.name"
                                    :value="item.warehouseID">
                            </el-option>
                        </el-select>
                    </el-form-item>

                    <el-form-item label="开单日期">
                        <el-date-picker
                                size="mini"
                                v-model="ruleForm.billDate"
                                align="right"
                                type="date"
                                placeholder="选择日期"
                                disabled
                        >
                        </el-date-picker>
                    </el-form-item>
                    <el-form-item label="审核日期">
                        <el-date-picker
                                size="mini"
                                v-model="ruleForm.verifyDate"
                                align="right"
                                type="date"
                                placeholder="选择日期"
                                value-format="yyyy-MM-dd"
                                :default-value="defaultValue"
                                :picker-options="pickerOptions"
                        >
                        </el-date-picker>
                    </el-form-item>
                    <el-form-item label="备注">
                        <el-input type="textarea" v-model="ruleForm.remark" disabled placeholder="请输入" size="mini"></el-input>
                    </el-form-item>
                </el-form>
            </div>
        </div>

        <div class="pad_10 backfff martp_15">
            <div class="pad_10 nav_header">
                <span class="font_14 marrt_20">调拨明细</span>
                <el-button type="primary" size="mini" icon="el-icon-document" @click="addProduct">新增</el-button>
                <el-button type="primary" size="mini" icon="el-icon-circle-close" @click="cancelProduct">取消
                </el-button>
                <el-button type="primary" size="mini" icon="el-icon-milk-tea" @click="deleteClick">删除</el-button>
                <!--<el-button type="primary" size="mini" icon="el-icon-s-claim">保存</el-button>-->
                <!--<el-button type="primary" size="mini" icon="el-icon-printer">打印</el-button>-->
                <!--<el-button type="primary" size="mini" icon="el-icon-s-unfold">导出</el-button>-->
                <!--<el-button type="primary" size="mini" icon="el-icon-s-fold">导入</el-button>-->
            </div>
            <div>
                <el-table :data="replenishmentList" ref="multipleTable" tooltip-effect="dark"
                          @selection-change="handleSelectionChange" max-height="400" style="width: 100%">
                    <el-table-column type="index" label="序号" width="55"></el-table-column>
                    <el-table-column type="selection" width="55"></el-table-column>
                    <el-table-column prop="name" label="名称" width="200">
                        <template slot-scope="scope">
                            <el-select class="width__fill_available" v-model="scope.row.isAdd" v-loadmore="loadMore"
                                       filterable remote reserve-keyword :remote-method="remoteMethod"
                                        placeholder="请选择" @change="productChange(scope.$index,scope.row)"
                            >
                                <el-option
                                        v-for="item in productList"
                                        :key="item.productID"
                                        :label="item.name"
                                        :value="item.productID">
                                </el-option>
                            </el-select>

                        </template>
                    </el-table-column>
                    <el-table-column prop="productID" label="货品编号"></el-table-column>
                     <el-table-column prop="Specification" label="规格"></el-table-column>
                    <el-table-column prop="inAmount" label="输入数量">
                        <template slot-scope="scope">
                            <el-input v-model="scope.row.inAmount"
                                      placeholder="请输入" @blur="numberChange(scope.$index,scope.row)"
                                      v-enter-number></el-input>
                        </template>
                    </el-table-column>
                    <el-table-column prop="inUnit" label="选择单位">
                        <template slot-scope="scope">
                            <el-select v-model="scope.row.inUnit" placeholder="请选择"
                                       @change="UnitChange(scope.$index,scope.row,$event)">
                                <el-option
                                        v-for="item in scope.row.barCodeList"
                                        :key="item.unit"
                                        :label="item.unit"
                                        :value="item.unit">
                                </el-option>
                            </el-select>
                        </template>
                    </el-table-column>
                    <el-table-column prop="maxPrice" label="选择单位单价（元）">
                        <template slot-scope="scope">
                            <el-input v-model="scope.row.maxPrice"
                                      placeholder="请输入" @change="priceChange(scope.$index,scope.row)"
                                        v-enter-number2></el-input>
                        </template>
                    </el-table-column>
                    <el-table-column prop="unit" label="最小单位"></el-table-column>
                    <el-table-column prop="price" label="最小单价（元）"></el-table-column>
                    <el-table-column prop="amount" label="最小单位数量合计"></el-table-column>
                    <el-table-column prop="quantityAmount" label="参考库存"></el-table-column>
                    <el-table-column prop="totalPrice" label="合计金额（元）"></el-table-column>
                </el-table>
            </div>
        </div>
    </div>
</template>

<script>
    import API from "@/api/allot";
    import util from "@/components/js/util"

    export default {
        data() {
            return {
                loading: false,
                selectLoading: false,
                modalLoading: false,
                billID: "",
                isConfirm: "", //单据状态
                selectPage: 1,
                selectTotal: 0,
                selectName: "",
                tableData: [],
                productID: "",
                warehouseList: [], //仓库列表
                productList: [], //产品列表
                barCodeList: [],//单位列表
                replenishmentList: [],
                multipleSelection: [],
                ruleForm: {
                    warehouseID: "", // 收货仓库
                    srcWarehouseID: "",  // 发货仓库
                    billDate: new Date(),
                    verifyDate: "",
                    remark: "",
                },
                rules: {
                    warehouseID: [{required: true, message: '请选择收货仓库', trigger: 'change'}],
                    srcWarehouseID: [{required: true, message: '请选择发货仓库', trigger: 'change'}],
                },
                defaultValue: "", //默认显示时间
                pickerOptions: {
                    disabledDate(time) {
                        return time.getTime() < Date.now() - 8.64e7 * 6 || time.getTime() > Date.now() + 8.64e7 * 6;
                    }
                }//时间配置
            };
        },
        methods: {
            // 单据状态
            formatStatus: function (row) {
                if (row.isConfirm == 0) {
                    return "未审核";
                } else if (row.isConfirm == 1) {
                    return "待发货";
                } else if (row.isConfirm == 3) {
                    return "待收货";
                } else {
                    return "已作废";
                }
            },
            // 取消
            cancelClick: function () {
                var that = this;
                that.$router.go(-1);
            },
            // 获取时间范围
            dateScope: function () {
                var that = this;
                API.getDateScope()
                    .then(res => {
                        if (res.stateCode == 100) {
                            var startDay = Math.floor((new Date().getTime() - res.data.AccountDate) / 86400000)
                            var endDay = Math.floor((new Date().getTime() - res.data.MaxDate) / 86400000)
                            that.defaultValue = new Date(res.data.AccountDate + 86400000);
                            that.pickerOptions = {
                                disabledDate(time) {
                                    return time.getTime() < Date.now() - 8.64e7 * startDay || time.getTime() > Date.now() - 8.64e7 * endDay;
                                }
                            }
                        } else {
                            that.$message.error({
                                message: res.message,
                                duration: 2000
                            });
                        }
                    })
            },
            // 单据作废
            cancellationClick: function () {
                var that = this;
                that.setConfirmClick(2)
            },
            // 确认审核
            affirmClick: function () {
                var that = this;
                that.setConfirmClick(1)
            },
            setConfirmClick: function (isConfirm) {
                var that = this;
                var params = "";
                var save = true;
                that.replenishmentList.forEach(function (item) {
                    if (item.productID == "") {
                        save = false;
                    }
                })
                if (isConfirm == 1) {
                    if (!save) {
                        that.$message.error({
                            message: "请填写必填项",
                            duration: 2000
                        });
                        return false;
                    }
                    console.log('ss',that.replenishmentList)
                    let flag=that.replenishmentList.findIndex(p=>p.amount<=0)
                    let flag1=that.replenishmentList.findIndex(p=>p.amount>p.quantityAmount)
                    if (flag!=-1){
                        that.$message.error({
                            message: "输入数量不能为0",
                            duration: 2000
                        });
                        return
                    }
                    if (flag1!=-1){
                        that.$message.error({
                            message: "输入数量不能大于参考库存",
                            duration: 2000
                        });
                        return
                    }
                    params = {
                        billID: that.billID,
                        isConfirm: isConfirm,
                        verifyDate: that.ruleForm.verifyDate,
                        billDate:that.ruleForm.billDate,
                        tedEntityList: that.replenishmentList,
                    }
                } else {
                    params = {
                        billID: that.billID,
                        isConfirm: isConfirm
                    }
                }
                that.modalLoading = true;
                API.setConfirm(params)
                    .then(res => {
                        if (res.stateCode == 100) {
                            that.$router.go(-1);
                        } else {
                            that.$message.error({
                                message: res.message,
                                duration: 2000
                            });
                        }
                    })
                    .finally(function () {
                        that.modalLoading = false;
                    });
            },
            // 仓库改变
            warehouseChange: function () {
                var that = this;
                that.replenishmentList = [];
            },
            // 仓库列表
            warehouseData: function () {
                var that = this;
                var params = {
                    Type: 2,
                }
                API.warehouseList(params)
                    .then(res => {
                        if (res.stateCode == 100) {
                            that.warehouseList = res.data;
                        } else {
                            that.$message.error({
                                message: res.message,
                                duration: 2000
                            });
                        }
                    })

            },

            // 产品列表
            productData: function () {
                var that = this;
                var params = {
                    pageNum: that.selectPage,
                    keyword: that.selectName
                }
                API.codeProductList(params)
                    .then(res => {
                        if (res.stateCode == 100) {
                            that.productList.push.apply(that.productList, res.data.list)
                            that.selectTotal = res.data.total;
                        } else {
                            that.$message.error({
                                message: res.message,
                                duration: 2000
                            });
                        }
                    })

            },
            // select 滑动选择
            loadMore: function () {
                var that = this;
                // that.selectLoading = true;
                that.selectPage++;
                if (that.selectPage * 10 <= that.selectTotal + 10) {
                    that.productData()
                }
            },

            // 远程搜索
            remoteMethod(query) {
                var that = this;
                that.productList = [];
                that.selectPage = 1;
                that.selectName = query.trim();
                that.selectLoading = true;
                setTimeout(() => {
                    that.selectLoading = false;
                    that.productData()
                }, 200);

            },
            // 数据显示
            handleSearch: function () {
                var that = this;
                that.loading = true;
                var params = {
                    billID: that.billID
                }
                API.allotApplyDetails(params)
                    .then(res => {
                        if (res.stateCode == 100) {
                            res.data.forEach(function (item) {
                                item.units.forEach(function (u) {
                                    u.amount = u.unitAmount
                                })
                                item.isAdd = item.productID;
                                item.barCodeList = item.units;
                                item.quantityAmount = item.stock;
                                item.unitAmount = item.unitAmount;
                                item.totalPrice = (item.inAmount * item.maxPrice).toFixed(2);
                            })
                            that.replenishmentList = res.data;
                        } else {
                            that.$message.error({
                                message: res.message,
                                duration: 2000
                            });
                        }
                    })
                    .finally(function () {
                        that.loading = false;
                    });
            },

            // 新增商品
            addProduct: function () {
                var that = this;
                that.selectPage = 1;
                that.productList = [];
                if (that.ruleForm.srcWarehouseID == "" || that.ruleForm.srcWarehouseID == null) {
                    that.$message.error({
                        message: "请选择发货仓库",
                        duration: 2000
                    });
                    return false;
                }
                that.productData();
                that.replenishmentList.unshift(
                    {
                        isAdd: "",
                        productID: "",
                        inAmount: 1,
                        quantityAmount: "必填",
                        maxPrice: "必填",
                        inUnit: "必填",
                        amount: "必填",
                        unit: "必填",
                        price: "必填",
                        totalPrice: "必填"
                    }
                )
            },
            priceDetailData: function (index) {
                var that = this;
                var params = {
                    warehouseID: that.ruleForm.srcWarehouseID,
                    productID: that.productID,
                }
                API.codeProductDetail(params)
                    .then(res => {
                        if (res.stateCode == 100) {
                            that.replenishmentList[index].barCodeList = res.data.barCodeList;
                            that.replenishmentList[index].productID = that.productID;
                            that.replenishmentList[index].inAmount = 1;
                            that.replenishmentList[index].quantityAmount = res.data.quantityAmount;
                            that.replenishmentList[index].maxPrice = (res.data.transPrice * res.data.amount).toFixed(2);
                            that.replenishmentList[index].inUnit = res.data.inUnit;
                            that.replenishmentList[index].amount = res.data.amount;
                            that.replenishmentList[index].unitAmount = res.data.amount;
                            that.replenishmentList[index].unit = res.data.unit;
                            that.replenishmentList[index].price = res.data.transPrice;
                            that.replenishmentList[index].totalPrice = (res.data.amount * res.data.transPrice).toFixed(2);
                        } else {
                            that.$message.error({
                                message: res.message,
                                duration: 2000
                            });
                        }
                    })

            },
            // 选择商品
            productChange: function (index, row) {
                var that = this;
                var addProduct = true;
                that.clearDetail(index);
                that.productID = row.isAdd;
                that.replenishmentList.forEach(function (item) {
                    if(item.productID == row.isAdd){
                        addProduct = false;
                    }
                })
                if(addProduct){
                    that.priceDetailData(index)
                }else {
                    that.$message.error({
                        message: "不能重复添加！",
                    });
                }
                 that.selectName = "";
                that.productData();
            },
                 // 清空详情数据
            clearDetail: function (index) {
                var that = this;
                that.replenishmentList[index].productID = "";
                that.replenishmentList[index].inAmount = 1;
                that.replenishmentList[index].quantityAmount = "必填";
                that.replenishmentList[index].maxPrice = "必填";
                that.replenishmentList[index].inUnit = "必填";
                that.replenishmentList[index].amount = "必填";
                that.replenishmentList[index].unit = "必填";
                that.replenishmentList[index].price = "必填";
                that.replenishmentList[index].totalPrice = "必填";
            },
            // 数量改变
            numberChange: function (index, row) {
                var that = this;
                that.replenishmentList[index].amount = row.unitAmount * row.inAmount;
                // that.replenishmentList[index].price = (row.maxPrice / row.unitAmount).toFixed(2);
                var totalPrice = (row.price * row.amount).toFixed(2);
                that.replenishmentList[index].totalPrice = totalPrice;
                // that.$set(that.replenishmentList[index], 'totalPrice', totalPrice)
            },
            // 默认单位改变
            UnitChange: function (index, row, value) {
                var that = this;
                var unitAmount = "";
                row.barCodeList.forEach(function (item) {
                    if (item.unit == value) {
                        unitAmount = item.amount
                    }
                })
                that.replenishmentList[index].unitAmount = unitAmount;
                that.replenishmentList[index].amount = unitAmount * row.inAmount;
                that.replenishmentList[index].maxPrice=(row.price * row.unitAmount).toFixed(2);
                var totalPrice = (row.price * row.amount).toFixed(2);
                that.replenishmentList[index].totalPrice = totalPrice;

            },

            // 最小单价改变
            priceChange: function (index, row) {
                var that = this;
                if(!(/^\d+(?:\.\d{1,4})?$/).test(row.maxPrice)){
                    that.$message.error({
                        message: "最多只能输入4位小数",
                    });
                    row.maxPrice = (row.maxPrice-0).toFixed(4)
                }
                that.replenishmentList[index].price = (row.maxPrice / row.unitAmount).toFixed(2);
                var totalPrice = (row.price * row.amount).toFixed(2);
                that.replenishmentList[index].totalPrice = totalPrice;
            },
            // 取消商品
            cancelProduct: function () {
                var that = this;
                that.replenishmentList.splice(0, 1);
            },
            // 保存
            saveClick: function () {
                var that = this;
                var save = true;
                that.replenishmentList.forEach(function (item) {
                    if (item.productID == "") {
                        save = false;
                    }
                })
                if (that.ruleForm.warehouseID == that.ruleForm.srcWarehouseID) {
                    that.$message.error({
                        message: "补货仓库、发货仓库不能重复",
                    });
                    return false;
                }
                if (that.replenishmentList.length == 0) {
                    that.$message.error({
                        message: "必须要新增至少1条明细才能保存",
                    });
                    return false;
                }
                // let flag=that.replenishmentList.findIndex(p=>p.amount<=0)
                // let flag1=that.replenishmentList.findIndex(p=>p.amount>p.quantityAmount)
                // if (flag!=-1){
                //     that.$message.error({
                //         message: "输入数量不能为0",
                //         duration: 2000
                //     });
                //     return
                // }
                // if (flag1!=-1){
                //     that.$message.error({
                //         message: "输入数量不能大于参考库存",
                //         duration: 2000
                //     });
                //     return
                // }
                if (!save) {
                    that.$message.error({
                        message: "请填写必填项",
                        duration: 2000
                    });
                    return false;
                }
                this.$refs.ruleForm.validate(valid => {
                    if (valid) {
                        that.modalLoading = true;
                        var params = {
                            billID: that.billID,
                            billDate: that.ruleForm.billDate,
                            verifyDate: that.ruleForm.verifyDate,
                            warehouseID: that.ruleForm.warehouseID,
                            srcWarehouseID: that.ruleForm.srcWarehouseID,
                            remark: that.ruleForm.remark,
                            tedEntityList: that.replenishmentList
                        }
                        API.editAllotApply(params)
                            .then(res => {
                                if (res.stateCode == 100) {
                                    that.$message({
                                        message: res.message,
                                    });
                                    that.$refs["ruleForm"].resetFields();
                                    that.replenishmentList = [];
                                    that.$router.go(-1)

                                } else {
                                    that.$message.error({
                                        message: res.message,
                                        duration: 2000
                                    });
                                }
                            })
                            .finally(function () {
                                that.modalLoading = false;
                            });
                    }
                })

            },
            // 删除
            deleteClick: function () {
                var that = this;
                if (that.multipleSelection == "") {
                    that.$message.error({
                        message: "请选择要删除的选项",
                        duration: 2000
                    });
                } else {
                    for (var i = 0; i < that.replenishmentList.length; i++) {
                        that.multipleSelection.forEach(function (item) {
                            if (that.replenishmentList[i].productID == item.productID) {
                                that.replenishmentList.splice(i, 1);
                            }
                        })
                    }
                }
            },
            // 多选
            handleSelectionChange(val) {
                this.multipleSelection = val;
            }
        },
        mounted() {
            var that = this;
            var detail = that.$route.query.detail;
            that.billID = detail.billID;
            that.ruleForm.warehouseID = detail.warehouseID || "";
            that.ruleForm.srcWarehouseID = detail.srcWarehouseID || "";
            that.ruleForm.billDate = detail.billDate || "";
            that.isConfirm = that.formatStatus(detail) || "";
            that.ruleForm.remark = detail.remark || "";
            if (that.formatStatus(detail)==='未审核'){
                that.ruleForm.verifyDate=new Date();
            }else{
                that.ruleForm.verifyDate = detail.verifyDate || "";
            }
            that.dateScope();
            that.handleSearch();
            that.warehouseData();
            that.productData();
        }
    };
</script>


<style scoped lang="scss">
</style>
