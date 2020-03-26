<template>
    <el-table :data="tableData" :span-method="objectSpanMethod" border style="width: 100%">
        <el-table-column prop="time" label="时间" width="180"></el-table-column>
        <el-table-column prop="name" label="姓名" width="180"></el-table-column>
        <el-table-column prop="age" label="年龄" width="180"></el-table-column>
        <el-table-column prop="sex" label="性别"></el-table-column>
    </el-table>
</template>

  <script>
export default {
    data() {
        return {
            tableData: [],
            timeObjNum: {}, //时间列对应的合并列数量
            nameObjNum: [] //姓名列对应的合并列数量
        };
    },
    methods: {
        setData() {
            let curData = [
                { time: "2020-01-01", name: "张1", age: 18, sex: "男" },
                { time: "2020-01-02", name: "张2", age: 12, sex: "女" },
                { time: "2020-01-03", name: "张4", age: 18, sex: "男" },
                { time: "2020-01-01", name: "张1", age: 32, sex: "女" },
                { time: "2020-01-03", name: "张3", age: 14, sex: "女" },
                { time: "2020-01-04", name: "张3", age: 18, sex: "男" },
                { time: "2020-01-01", name: "张2", age: 15, sex: "女" },
                { time: "2020-01-02", name: "张3", age: 14, sex: "男" },
                { time: "2020-01-02", name: "张3", age: 19, sex: "男" }
            ];

            // 将数据按照想要合并的列，进行排序
            // time、name

            curData.sort(this.compare("time"));

            // 在时间排序的基础上，对姓名再进行排序

            let rowValue = [];
            let timeObj = [];
            curData.map(value => {
                rowValue.push(value.time);
                timeObj[value.time] = { start: 0, length: 0 };
            });

            rowValue.map((value, index) => {
                if (timeObj[value].length == 0) {
                    timeObj[value].start = index;
                }
                timeObj[value].length++;
            });

            this.timeObjNum = timeObj;

            //在时间排好序的基础上，对姓名进行排序
            let curArr = [];
            let nameObj = [];
            let curNameObj = [];

            let curIndex = 0;
            Object.keys(timeObj).map(value => {
                // 将表格数据按照时间分开排序
                let timeArr = [];
                curData.map((val, index) => {
                    if (val["time"] == value) {
                        timeArr.push(val);
                    }
                });

                curArr.push(...timeArr.sort(this.compare("name")));

                // 遍历表格数据，确定姓名的起始位置
                let nameArr = [];
                timeArr.map((timeVal, tiemIndex) => {
                    nameArr.push(timeVal["name"]);
                    curNameObj[curIndex + tiemIndex] = {
                        start: curIndex + tiemIndex,
                        length: 0
                    };
                });

                let firstName = "";
                timeArr.map((timeVal, tiemIndex) => {
                    if (firstName != timeVal["name"]) {
                        firstName = timeVal["name"];
                        curNameObj[
                            curIndex + tiemIndex
                        ].length = this.checkArray(timeVal["name"], nameArr);
                    } else {
                        curNameObj[curIndex + tiemIndex].length = 0;
                    }
                });

                this.nameObjNum = curNameObj;

                curIndex += timeObj[value].length;
            });

            this.tableData = curArr;
        },

        compare(prop) {
            return function(obj1, obj2) {
                let firstVal = obj1[prop];
                let secondVal = obj2[prop];
                if (firstVal < secondVal) {
                    return -1;
                } else if (firstVal > secondVal) {
                    return 1;
                } else {
                    return 0;
                }
            };
        },

        checkArray(para, arr) {
            let processArr = arr.filter(function(value) {
                return value == para;
            });

            return processArr.length; // 这里返回数组长度或者相应处理
        },

        objectSpanMethod({ row, column, rowIndex, columnIndex }) {
            // 行，列，行下标，列下标
            // console.log(row, column, rowIndex, columnIndex);

            // 根据column.property 判断对哪几列进行合并
            // time、name
            if (column.property == "time") {
                if (this.timeObjNum[row["time"]].start == rowIndex) {
                    return {
                        rowspan: this.timeObjNum[row["time"]].length,
                        colspan: 1
                    };
                } else {
                    return {
                        rowspan: 0,
                        colspan: 0
                    };
                }
            }

            if (column.property == "name") {
                if (this.nameObjNum[rowIndex].length == 0) {
                    return {
                        rowspan: 0,
                        colspan: 0
                    };
                } else {
                    return {
                        rowspan: this.nameObjNum[rowIndex].length,
                        colspan: 1
                    };
                }
            }
        }
    },
    created() {
        this.setData();
    }
};
</script>

<style lang="less" scoped>
</style>