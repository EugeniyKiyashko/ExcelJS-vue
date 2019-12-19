<template>
  <div>
    <h2 class="content-block">Display Data</h2>

    <dx-data-grid
      class="dx-card wide-card"
      :data-source="dataSourceConfig"
      :focused-row-index="0"
      :show-borders="false"
      :focused-row-enabled="true"
      :column-auto-width="true"
      :column-hiding-enabled="true"
      @exporting="exporting"
    >
      <dx-paging :page-size="10" />
      <dx-pager :show-page-size-selector="true" :show-info="true" />
      <dx-filter-row :visible="true" />

      <dx-column data-field="Task_ID" :width="90" :hiding-priority="2" />

      <dx-column
        data-field="Task_Subject"
        caption="Subject"
        :width="190"
        :hiding-priority="8"
      />

      <dx-column
        data-field="Task_Status"
        caption="Status"
        :hiding-priority="6"
      />

      <dx-column
        data-field="Task_Priority"
        caption="Priority"
        :hiding-priority="5"
      >
        <dx-lookup
          display-expr="name"
          value-expr="value"
          :data-source="priorities"
        />
      </dx-column>

      <dx-column
        data-field="ResponsibleEmployee.Employee_Full_Name"
        caption="Assigned To"
        :allow-sorting="false"
        :hiding-priority="7"
      />

      <dx-column
        data-field="Task_Start_Date"
        caption="Start Date"
        data-type="date"
        :hiding-priority="3"
      />

      <dx-column
        data-field="Task_Due_Date"
        caption="Due Date"
        data-type="date"
        :hiding-priority="4"
      />

      <dx-column
        data-field="Task_Priority"
        caption="Priority"
        :hiding-priority="1"
      />

      <dx-column
        data-field="Task_Completion"
        caption="Completion"
        :hiding-priority="0"
      />

      <dx-export
        :enabled="true"
      />

    </dx-data-grid>
  </div>
</template>

<script>
import "devextreme/data/odata/store";
import DxDataGrid, {
  DxColumn,
  DxFilterRow,
  DxLookup,
  DxPager,
  DxPaging,
  DxExport
} from "devextreme-vue/data-grid";
import { exportDataGrid } from 'devextreme/exporter/exceljs/excelExporter';
import ExcelJS from 'exceljs';
import saveAs from 'file-saver';

const priorities = [
  { name: "High", value: 4 },
  { name: "Urgent", value: 3 },
  { name: "Normal", value: 2 },
  { name: "Low", value: 1 }
];

export default {
  data() {
    return {
      priorities
    };
  },
  created() {
    this.dataSourceConfig = {
      store: {
        type: "odata",
        key: "Task_ID",
        url: "https://js.devexpress.com/Demos/DevAV/odata/Tasks"
      },
      expand: "ResponsibleEmployee",
      select: [
        "Task_ID",
        "Task_Subject",
        "Task_Start_Date",
        "Task_Due_Date",
        "Task_Status",
        "Task_Priority",
        "Task_Completion",
        "ResponsibleEmployee/Employee_Full_Name"
      ]
    };
  },
  methods: {
    exporting(e) {
      var workbook = new ExcelJS.Workbook();
      var worksheet = workbook.addWorksheet('Main sheet');

      exportDataGrid({
        component: e.component,
        worksheet: worksheet,
        topLeftCell: { row: 3, column: 3 },
      }).then(function() {
        workbook.xlsx.writeBuffer().then(function(buffer) {
          saveAs(new Blob([buffer], { type: 'application/octet-stream' }), 'DataGrid.xlsx');
        });
      });
      e.cancel = true;
    }
  },
  components: {
    DxDataGrid,
    DxColumn,
    DxFilterRow,
    DxLookup,
    DxPager,
    DxPaging,
    DxExport
  }
};
</script>
