<template>
    <div class="role-create">
        <el-form :model="form" :rules="rules" ref="createRole" label-width="75px">
            <el-form-item prop="name" label="角色名称">
                <el-input v-model="form.name"></el-input>
            </el-form-item>
            <el-form-item label="角色状态">
                <el-switch
                    class="tablescope"
                    v-model="form.status_name"
                    active-color="#409EFF"
                    inactive-color="#dcdfe6"
                    active-text="开启"
                    inactive-text="关闭"
                ></el-switch>
            </el-form-item>
            <el-form-item prop="menu_ids" label="角色权限">
                <el-tree :data="menus" show-checkbox node-key="id" ref="tree" :default-expand-all="true" :props="defaultProps"></el-tree>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="saveCreate" :loading="loading">确定添加</el-button>
            </el-form-item>
        </el-form>
    </div>
</template>

<script>
import { menuTree } from '../../api/menu';
import { createRole } from '../../api/role';
export default {
    name: 'createRole',
    data() {
        return {
            loading: false,
            query: {},
            menus: [],
            defaultProps: {
                label: 'name',
                children: 'children'
            },
            form: {
                name: '',
                status: '',
                status_name: true,
                menu_ids: []
            },
            rules: {
                name: [{ required: true, message: '请输入角色名称', trigger: 'blur' }],
                menu_ids: [{ required: true, message: '请选择权限', trigger: 'blur' }]
            }
        };
    },
    created() {
        this.getData();
    },
    methods: {
        // 获取菜单列表
        getData() {
            menuTree(this.query).then((res) => {
                this.menus = res.data;
            });
        },
        // 保存添加
        saveCreate() {
            this.form.menu_ids = this.$refs.tree.getCheckedKeys();
            this.$refs.createRole.validate((valid) => {
                if (valid) {
                    this.loading = true;
                    if (this.form.status_name == true) {
                        this.form.status = 1;
                    } else {
                        this.form.status = 0;
                    }
                    var data = {
                        id: this.form.id,
                        name: this.form.name,
                        status: this.form.status,
                        menu_ids: this.$refs.tree.getCheckedKeys().concat(this.$refs.tree.getHalfCheckedKeys())
                    };
                    createRole(data).then((res) => {
                        if (res.code == 200) {
                            this.$message.success('添加成功');
                            this.$emit('on-success', true);
                        } else {
                            this.$message.error(res.msg);
                            this.loading = false;
                        }
                    });
                }
            });
        }
    }
};
</script>

<style lang="scss">
.tablescope {
    .el-switch__label--left {
        position: relative;
        left: 55px;
        color: #fff;
        z-index: -100;
    }
    .el-switch__core {
        width: 55px !important;
    }
    .el-switch__label--right {
        position: relative;
        right: 55px;
        color: #fff;
        z-index: -100;
    }
    .el-switch__label--right.is-active {
        z-index: 100;
        color: #fff !important;
    }
    .el-switch__label--left.is-active {
        z-index: 100;
        color: #777777 !important;
    }
    .el-switch__label * {
        font-size: 12px;
    }
}
.role-create .el-switch {
    margin-left: -30px;
}
</style>