<template>
  <ms-edit-dialog
    :append-to-body="true"
    :visible.sync="visible"
    :title="$t('test_track.case.relate_issue')"
    @confirm="save"
    ref="relevanceDialog">
    <ms-search
      :base-search-tip="$t('commons.search_by_name_or_id')"
      :condition.sync="page.condition"
      @search="getIssues"/>
    <ms-table
      v-loading="page.result.loading"
      :data="page.data"
      :condition="page.condition"
      :total="page.total"
      :page-size.sync="page.pageSize"
      :show-select-all="false"
      @handlePageChange="getIssues"
      @refresh="getIssues"
      ref="table">

      <ms-table-column
        :label="$t('test_track.issue.id')"
        prop="id" v-if="false">
      </ms-table-column>
      <ms-table-column
        :label="$t('test_track.issue.id')"
        prop="num">
      </ms-table-column>

      <ms-table-column
        :label="$t('test_track.issue.title')"
        prop="title"
        min-width="200px">
      </ms-table-column>

      <ms-table-column
        :label="$t('test_track.issue.platform_status')"
        v-if="isThirdPart"
        prop="platformStatus">
        <template v-slot="scope">
          {{ scope.row.platformStatus ? scope.row.platformStatus : '--'}}
        </template>
      </ms-table-column>

      <ms-table-column
        v-else
        :label="$t('test_track.issue.status')"
        prop="status">
        <template v-slot="scope">
          <span>{{ issueStatusMap[scope.row.status] ? issueStatusMap[scope.row.status] : scope.row.status }}</span>
        </template>
      </ms-table-column>

      <ms-table-column
        :label="$t('test_track.issue.platform')"
        prop="platform">
      </ms-table-column>

      <issue-description-table-item/>

    </ms-table>

    <ms-table-pagination :change="getIssues" :current-page.sync="page.currentPage" :page-size.sync="page.pageSize" :total="page.total"/>

  </ms-edit-dialog>
</template>
<style scoped>
.search-input {
  float: right;
  width: 300px;
}
</style>
<script>
import MsEditDialog from "@/business/components/common/components/MsEditDialog";
import MsTable from "@/business/components/common/components/table/MsTable";
import MsTableColumn from "@/business/components/common/components/table/MsTableColumn";
import {getRelateIssues, isThirdPartEnable, testCaseIssueRelate} from "@/network/Issue";
import IssueDescriptionTableItem from "@/business/components/track/issue/IssueDescriptionTableItem";
import {ISSUE_STATUS_MAP} from "@/common/js/table-constants";
import MsTablePagination from "@/business/components/common/pagination/TablePagination";
import {getPageInfo} from "@/common/js/tableUtils";
import {getCurrentProjectID} from "@/common/js/utils";
import {
  TEST_CASE_RELEVANCE_ISSUE_LIST
} from "@/business/components/common/components/search/search-components";
import MsSearch from "@/business/components/common/components/search/MsSearch";
export default {
  name: "IssueRelateList",
  components: {MsTablePagination, IssueDescriptionTableItem, MsTableColumn, MsTable, MsEditDialog, MsSearch},
  data() {
    return {
      page: getPageInfo({
        components: TEST_CASE_RELEVANCE_ISSUE_LIST
      }),
      visible: false,
      isThirdPart: false
    }
  },
  computed: {
    issueStatusMap() {
      return ISSUE_STATUS_MAP;
    },
    projectId() {
      return getCurrentProjectID();
    }
  },
  props: {
    caseId: String,
    planCaseId: String,
    notInIds: Array,
  },
  created() {
    isThirdPartEnable((data) => {
      this.isThirdPart = data;
    });
  },
  methods: {
    open() {
      this.getIssues();
      this.visible = true;
    },
    getIssues() {
      this.page.condition.projectId = this.projectId;
      this.page.condition.notInIds = this.notInIds;
      this.page.result = getRelateIssues(this.page);
    },
    getCaseResourceId() {
      return this.planCaseId ? this.planCaseId : this.caseId;
    },
    save() {
      let param = {};
      param.issueIds = Array.from(this.$refs.table.selectRows).map(i => i.id);
      param.caseResourceId = this.getCaseResourceId();
      param.isPlanEdit = !!this.planCaseId;
      param.refId = this.planCaseId ? this.caseId : null;
      testCaseIssueRelate(param, () => {
        this.visible = false;
        this.$emit('refresh', this.$refs.table.selectRows);
      });
    }
  }
}
</script>

<style scoped>

</style>
