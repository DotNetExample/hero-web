<template>
  <div>
    <el-row>
      <el-col>
        <sticky
          :z-index="10"
          class-name="sub-navbar"
          style="margin-bottom: 10px"
        >
          <el-button
            style="margin-left: 10px"
            type="success"
            @click="handleUpdate()"
            >保存</el-button
          >
          <el-button
            style="margin-left: 10px"
            type="warning"
            @click="handleCancel()"
            >取消</el-button
          >
        </sticky>
      </el-col>
      <el-col :span="14" :push="5">
        <userGroup-page-form
          ref="userGroup-form"
          :userGroup="userGroup"
        ></userGroup-page-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import { mapActions } from "vuex";
import waves from "@/directive/waves"; // waves directive
import OrgNode from "@/views/organization/components/OrgNode.vue";
import UserGroupPageForm from "./components/UserGroupPageForm";
import Sticky from "@/components/Sticky"; // 粘性header组件
import { Loading } from "element-ui";
export default {
  components: { UserGroupPageForm, Sticky, Loading },
  data() {
    return {
      userGroup: {
        orgIds: undefined,
        name: undefined,
        memo: undefined,
        dataPermissionOrgIds: [],
        permissionIds: [],
      },
    };
  },
  mounted() {
    if (this.$route.query.id) {
      this.getUserGroup(this.$route.query.id).then((data) => {
        this.userGroup = data;
        this.$nextTick(() => {
          this.$refs["userGroup-form"].roles = data.roles;
          this.$refs["userGroup-form"].$refs["userGroup"].clearValidate();
        });
      });
    }
  },
  methods: {
    ...mapActions("userGroup", ["updateUserGroup", "getUserGroup"]),
    handleUpdate() {
      this.$refs["userGroup-form"].$refs["userGroup"].validate((valid) => {
        if (valid) {
          let loadingInstance = Loading.service({
            target: ".el-dialog",
            text: "保存中...",
          });
          this.updateUserGroup(this.userGroup)
            .then((data) => {
              this.$notify({
                title: "成功",
                message: data,
                type: "success",
                duration: 2000,
              });
              this.$nextTick(() => {
                // 以服务的方式调用的 Loading 需要异步关闭
                loadingInstance.close();
              });
              this.$store.dispatch("tagsView/delView", this.$route);
              this.$router.push({ name: "usergroup" });
            })
            .catch((err) => {
              this.$nextTick(() => {
                // 以服务的方式调用的 Loading 需要异步关闭
                loadingInstance.close();
              });
            });
        }
      });
    },
    handleCancel() {
      this.$store.dispatch("tagsView/delView", this.$route);
      this.$router.go(-1);
    },
  },
};
</script>

<style>
</style>