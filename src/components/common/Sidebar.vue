<template>
    <div class="sidebar">
        <!-- <el-aside> -->
        <!--<div class="title">{{siteName}}</div>-->
        <el-menu
                class="sidebar-el-menu"
                :default-active="onRoutes"
                :collapse="collapse"
                text-color="#bfcbd9"
                active-text-color="#20a0ff"
                :unique-opened="true"
                router
        >

            <template v-for="item in items">
                <template v-if="item.children.length>0">
                    <el-submenu :index="item.key" :key="item.id">
                        <template slot="title">
                            <i :class="item.icon"></i>
                            <span slot="title">{{ item.text }}</span>
                        </template>
                        <template v-for="subItem in item.children">
                            <el-submenu v-if="subItem.children.length>0" :index="subItem.key" :key="subItem.id">
                                <template slot="title">{{ subItem.text }}</template>
                                <el-menu-item
                                        v-for="(threeItem,i) in subItem.children"
                                        :key="i"
                                        :index="threeItem.key"
                                >{{ threeItem.text }}
                                </el-menu-item>
                            </el-submenu>
                            <el-menu-item v-else :index="subItem.key" :key="subItem.id">{{ subItem.text }}
                            </el-menu-item>
                        </template>
                    </el-submenu>
                </template>
                <template v-else>
                    <el-menu-item :index="item.key" :key="item.id">
                        <i :class="item.icon"></i>
                        <span slot="title">{{ item.text }}</span>
                    </el-menu-item>
                </template>
            </template>
        </el-menu>
        <!-- </el-aside> -->
    </div>
</template>

<script>
    import bus from "@/components/js/bus";
    import API from "@/api/slider";

    export default {
        data() {
            return {
                collapse: false,
                siteName: "",
                items: []
            };
        },
        computed: {
            onRoutes() {
                var that = this;
                if (that.$route.meta.supPath) {
                    return that.$route.meta.supPath.replace("#", "");
                } else {
                    return that.$route.path.replace("#", "");
                }
            },
        },
        created() {
            // 通过 Event Bus 进行组件间通信，来折叠侧边栏
            bus.$on("collapse", msg => {
                this.collapse = msg;
            });
        },
        methods: {
            sliderData: function () {
                var that = this;
                API.getJurisdiction()
                    .then(res => {
                        if (res.stateCode == 100) {
                            console.log('res',res);

                            that.items = res.data.children

                        } else {
                            that.$message.error({
                                message: res.message,
                                duration: 2000
                            });
                        }
                    })
            },
        },
        mounted() {
            var that = this;
            that.siteName = JSON.parse(localStorage.getItem('access-user')).siteName;
            that.sliderData()
        }
    };
</script>

<style scoped lang="scss">
    .sidebar {
        display: block;
        position: absolute;
        left: 0;
        top: 50px;
        bottom: 0;
        overflow-y: scroll;
        .title {
            padding: 15px;
            text-align: center;
            background-color: #d1dadf;
        }
    }

    .sidebar::-webkit-scrollbar {
        width: 0;
    }

    .sidebar-el-menu:not(.el-menu--collapse) {
        width: 180px;
    }

    .sidebar {
        ul {
            height: 100%;
        }
    }

    .sidebar {
        .sidebar-el-menu {
            .el-menu-item {
                color: #000 !important;
            }
            .el-submenu {
                .el-submenu__title {
                    color: #000 !important;
                }
            }

        }
    }

    .sidebar-el-menu {
        .is-opened {
            .is-active {
                color: #3296FA !important;
                background-color: #F4FBFF;

            }

        }
    }

    .el-menu--vertical {
        .el-menu {
            li {
                color: #333 !important;
            }
        }
    }

</style>
