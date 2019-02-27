<template>
    <div class="sidebar">
        <el-menu class="sidebar-el-menu" :default-active="onRoutes" :collapse="collapse" background-color="#324157"
            text-color="#bfcbd9" active-text-color="#20a0ff" unique-opened router>
            <template v-for="item in items">
                <template v-if="item.subs">
                    <el-submenu :index="item.index" :key="item.index">
                        <template slot="title">
                            <i :class="item.icon"></i><span slot="title">{{ item.title }}</span>
                        </template>
                        <template v-for="subItem in item.subs">
                            <el-submenu v-if="subItem.subs" :index="subItem.index" :key="subItem.index">
                                <template slot="title">{{ subItem.title }}</template>
                                <el-menu-item v-for="(threeItem,i) in subItem.subs" :key="i" :index="threeItem.index">
                                    {{ threeItem.title }}
                                </el-menu-item>
                            </el-submenu>
                            <el-menu-item v-else :index="subItem.index" :key="subItem.index">
                                {{ subItem.title }}
                            </el-menu-item>
                        </template>
                    </el-submenu>
                </template>
                <template v-else>
                    <el-menu-item :index="item.index" :key="item.index">
                        <i :class="item.icon"></i><span slot="title">{{ item.title }}</span>
                    </el-menu-item>
                </template>
            </template>
        </el-menu>
    </div>
</template>

<script>
    import bus from '../common/bus';
    export default {
        data() {
            return {
                collapse: false,
                items: [
                    {
                        icon: 'el-icon-lx-home',
                        index: 'dashboard',
                        title: '系统首页'
                    },
                    {
                        icon: 'el-icon-lx-rechargefill',
                        index: 'salary',
                        title: '营业额',
                        subs: [
                            {
                                index: 'charts',
                                title: '每日营业额'
                            },
                            {
                                index: '1',
                                title: '每月营业额',
                            },
                            {
                                index: '2',
                                title: '年度营业额'
                            }
                        ]
                    },
                    /*
                      * index 跳转我们自己定义的vue 模块
                      * */
                    {
                        icon: 'el-icon-lx-calendar',
                        index: '3',
                        title: '管理模块',
                        subs: [
                            {
                                index: 'RoomDetails',
                                title: '房屋详情',
                                subs: [
                                    {
                                        index: 'RentalMode',
                                        title: '出租方式',
                                    },
                                    {
                                        index: 'RoomType',
                                        title: '民宿类型',
                                    },
                                    {
                                        index: 'BedType',
                                        title: '床型'
                                    },
                                    {
                                        index: 'BedParams',
                                        title: '床型参数'
                                    },
                                    {
                                        index: 'LodgerRule',
                                        title: '房客要求'
                                    }
                                ]
                            },
                            {
                                index: 'FacilityService',
                                title: '设施服务',
                                subs: [
                                    {
                                        index: 'HouseFacilities',
                                        title: '设施服务类型',
                                    },
                                    {
                                        index: 'HouseFacilitiesDetails',
                                        title: '设施服务详细',
                                    }
                                ]
                            }
                        ]
                    },
                    {
                        icon: 'el-icon-lx-rankfill',
                        index: 'database',
                        title: '数据库',
                        subs: [
                            {
                                index: 'databaseControl',
                                title: '数据库备份'
                            },
                            {
                                index: '3',
                                title: '数据迁移',
                            }
                        ]
                    },
                    {
                        icon: 'el-icon-edit',
                        index: '4',
                        title: '日志记录'
                    },

                    {
                        icon: 'el-icon-lx-cascades',
                        index: 'table',
                        title: '基础表格'
                    },
                    {
                        icon: 'el-icon-lx-copy',
                        index: 'RegsistAdmin',
                        title: '管理员管理'
                    },

                    {
                        icon: 'el-icon-lx-emoji',
                        index: 'icon',
                        title: '自定义图标'
                    },

                    /*{
                        icon: 'el-icon-rank',
                        index: '6',
                        title: '拖拽组件',
                        subs: [
                            {
                                index: 'drag',
                                title: '拖拽列表',
                            },
                            {
                                index: 'dialog',
                                title: '拖拽弹框',
                            }
                        ]
                    },
                    {
                        icon: 'el-icon-lx-warn',
                        index: '7',
                        title: '错误处理',
                        subs: [
                            {
                                index: 'permission',
                                title: '权限测试'
                            },
                            {
                                index: '404',
                                title: '404页面'
                            }
                        ]
                    }*/
                ]
            }
        },
        computed:{
            onRoutes(){
                return this.$route.path.replace('/','');
            }
        },
        created(){
            // 通过 Event Bus 进行组件间通信，来折叠侧边栏
            bus.$on('collapse', msg => {
                this.collapse = msg;
            })
        }
    }
</script>

<style scoped>
    .sidebar{
        display: block;
        position: absolute;
        left: 0;
        top: 70px;
        bottom:0;
        overflow-y: scroll;
    }
    .sidebar::-webkit-scrollbar{
        width: 0;
    }
    .sidebar-el-menu:not(.el-menu--collapse){
        width: 250px;
    }
    .sidebar > ul {
        height:100%;
    }
</style>
