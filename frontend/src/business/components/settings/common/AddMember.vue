<template>
  <el-dialog :close-on-click-modal="false" :title="$t('member.create')" :visible.sync="dialogVisible" width="45%"
             :destroy-on-close="true"
             @close="close" v-loading="result.loading">
    <el-form :model="form" ref="form" :rules="rules" label-position="right" label-width="80px" size="small">
      <el-form-item :label="$t('commons.member')" prop="userIds"
                    :rules="{required: true, message: $t('member.please_choose_member'), trigger: 'blur'}">
        <el-select
          v-model="form.userIds"
          multiple
          filterable
          @visible-change="visibleChange"
          :filter-method="userFilter"
          :popper-append-to-body="false"
          class="member_select"
          :placeholder="$t('member.please_choose_member')">
          <el-option
            v-for="item in userList"
            :key="item.id"
            :label="item.id"
            :value="item.id">
            <user-option-item :user="item"/>
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item :label="$t('commons.group')" prop="groupIds">
        <el-select v-model="form.groupIds" multiple :placeholder="$t('group.please_select_group')" class="group_select">
          <el-option
            v-for="item in form.groups"
            :key="item.id"
            :label="item.name"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
    </el-form>
    <template v-slot:footer>
      <div class="dialog-footer">
        <el-button @click="dialogVisible = false" size="medium">{{ $t('commons.cancel') }}</el-button>
        <el-button type="primary" @click="submitForm('form')" size="medium" @keydown.enter.native.prevent>
          {{ $t('commons.confirm') }}
        </el-button>
      </div>
    </template>
  </el-dialog>
</template>

<script>

import UserOptionItem from "@/business/components/settings/common/UserOptionItem";

export default {
  name: "AddMember",
  components: {UserOptionItem},
  data() {
    return {
      dialogVisible: false,
      form: {},
      rules: {
        userIds: [
          {required: true, message: this.$t('member.please_choose_member'), trigger: ['blur']}
        ],
        groupIds: [
          {required: true, message: this.$t('group.please_select_group'), trigger: ['blur']}
        ]
      },
      userList: [],
      copyUserList: [],
      result: {}
    }
  },
  props: {
    groupType: {
      type: String,
      default() {
        return '';
      }
    },
    groupScopeId: {
      type: String,
      default() {
        return '';
      }
    },
    userResourceUrl: {
      type: String,
      default() {
        return '/user/list/';
      }
    }
  },
  methods: {
    visibleChange(val) {
      if (!val) {
        this.userFilter(null);
      }
    },
    submitForm() {
      this.$refs['form'].validate((valid) => {
        if (valid) {
          let param = {
            userIds: this.form.userIds,
            groupIds: this.form.groupIds,
          };
          this.$emit("submit", param);
        }
      });
    },
    open() {
      this.dialogVisible = true;
      this.result = this.$get(this.userResourceUrl, response => {
        this.userList = response.data;
        this.copyUserList = response.data;
      })
      this.result = this.$post('/user/group/list', {type: this.groupType, resourceId: this.groupScopeId}, response => {
        this.$set(this.form, "groups", response.data);
      })
    },
    close() {
      this.dialogVisible = false;
      this.form = {};
    },
    userFilter(val) {
      if (val) {
        this.userList = this.copyUserList.filter((item) => {
          if (!!~item.id.indexOf(val) || (item.name && !!~item.name.indexOf(val))) {
            return true;
          }
        })
      } else {
        this.userList = this.copyUserList;
      }
    }
  }
}
</script>

<style scoped>
.member_select, .group_select {
  display: block;
}
</style>
