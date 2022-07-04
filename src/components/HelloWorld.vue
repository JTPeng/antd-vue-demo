<template>
  <div>
    {{ data }}
    <a-table
      ref="sortTable"
      class="sort-table"
      :columns="columns"
      :data-source="data"
      :row-selection="rowSelection"
      :expanded-row-keys.sync="expandedRowKeys"
    />
  </div>
</template>
<script>
import Sortable from "sortablejs";
const columns = [
  {
    title: "Name",
    dataIndex: "name",
    key: "name",
  },
  {
    title: "Age",
    dataIndex: "age",
    key: "age",
    width: "12%",
  },
  {
    title: "Address",
    dataIndex: "address",
    width: "30%",
    key: "address",
  },
];

const data = [
  {
    key: 1,
    name: "John Brown sr.",
    age: 60,
    address: "New York No. 1 Lake Park",
    children: [
      {
        key: 11,
        name: "John Brown",
        age: 42,
        address: "New York No. 2 Lake Park",
      },
      {
        key: 12,
        name: "John Brown jr.",
        age: 30,
        address: "New York No. 3 Lake Park",
        children: [
          {
            key: 121,
            name: "Jimmy Brown",
            age: 16,
            address: "New York No. 3 Lake Park",
          },
        ],
      },
      {
        key: 13,
        name: "Jim Green sr.",
        age: 72,
        address: "London No. 1 Lake Park",
        children: [
          {
            key: 131,
            name: "Jim Green",
            age: 42,
            address: "London No. 2 Lake Park",
            children: [
              {
                key: 1311,
                name: "Jim Green jr.",
                age: 25,
                address: "London No. 3 Lake Park",
              },
              {
                key: 1312,
                name: "Jimmy Green sr.",
                age: 18,
                address: "London No. 4 Lake Park",
              },
            ],
          },
        ],
      },
    ],
  },
  {
    key: 2,
    name: "Joe Black",
    age: 32,
    address: "Sidney No. 1 Lake Park",
  },
];

const rowSelection = {
  onChange: (selectedRowKeys, selectedRows) => {
    console.log(
      `selectedRowKeys: ${selectedRowKeys}`,
      "selectedRows: ",
      selectedRows
    );
  },
  onSelect: (record, selected, selectedRows) => {
    console.log(record, selected, selectedRows);
  },
  onSelectAll: (selected, selectedRows, changeRows) => {
    console.log(selected, selectedRows, changeRows);
  },
};

export default {
  data() {
    return {
      data,
      columns,
      rowSelection,
      expandedRowKeys: [],
      pullIndex: "",
    };
  },
  mounted() {
    this.initSortable();
  },
  methods: {
    initSortable() {
      const el = this.$el.querySelector(".sort-table tbody");
      const _this = this;
      Sortable.create(el, {
        handle: ".ant-table-row",
        animation: 150,
        group: { name: "name", pull: true, put: true },
        onUpdate(evt) {
          const oldIndex = evt.oldIndex;
          const newIndex = evt.newIndex;
          if (oldIndex === newIndex) return;
          _this.sortListAndUpdate(_this.data, oldIndex, newIndex);
          console.log("oldIndex", oldIndex);
          console.log("newIndex", newIndex);
        },
        onStart(evt) {
          console.log("onStart", evt);
        },
        onAdd(evt) {
          console.log("onAdd", evt);
        },
        onRemove(evt) {
          console.log("onRemove", evt);
        },
      });
    },
    sortList(list, oldIndex, newIndex) {
      const newTableData = JSON.parse(JSON.stringify(list));
      const data = newTableData.splice(oldIndex, 1, null);
      newTableData.splice(
        oldIndex < newIndex ? newIndex + 1 : newIndex,
        0,
        data[0]
      );
      newTableData.splice(oldIndex > newIndex ? oldIndex + 1 : oldIndex, 1);
      return newTableData;
    },
    sortListAndUpdate(list, oldIndex, newIndex) {
      const newTableData = this.sortList(list, oldIndex, newIndex);
      // newTableData.forEach((item, index) => {
      //   item.sort = index + 1;
      // });
      this.$nextTick(() => {
        this.data = newTableData;
        console.log("object", this.$refs.sortTable);
        // this.$refs.sortTable && this.$refs.sortTable.refresh(true);
      });
    },
  },
};
</script>
