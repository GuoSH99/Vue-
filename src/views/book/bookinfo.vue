<template>
  <div>
    <el-form
      ref="searchForm"
      :inline="true"
      :model="searchWhewe"
      class="demo -form-inline"
      style="margin-top: 20px"
    >
      <el-form-item label="书名" size="mini" prop="bookName">
        <el-input
          v-model="searchWhewe.bookName"
          placeholde
          r="书名"
          style="width: 150px"
        ></el-input>
      </el-form-item>
      <el-form-item label="出版社" size="mini" prop="press" @click.right.prevent.native="dialogPressVisible=true">
        <el-input
          v-model="searchWhewe.press"
          placeholder="出版社"
          style="width: 150px"
        ></el-input>
      </el-form-item>
      <el-form-item label="图书类型" size="mini" prop="bookType">
        <!-- bookTypeOptions 要绑定到 data 中才会生效，否则找不 到 bookTypeOptions -->
        <el-select
          v-model="searchWhewe.bookType"
          placeholder="图书类型"
          style="width: 120px"
        >
          <el-option
            v-for="option in bookTypeOptions"
            :key="option.type"
            :label="option.name"
            :value="option.type"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="出版日期" size="mini" prop="publicationdate">
        <el-date-picker
          size="mini"
          style="width: 225px"
          v-model="searchWhewe.publicationdate"
          type="daterange"
          align="right"
          unlink-panels
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          :picker-options="pickerOptions"
        >
        </el-date-picker>
      </el-form-item>
      <el-form-item size="mini">
        <el-button type="primary" @click="fetchData">查询 </el-button>
        <el-button @click="resetForm('searchForm')">重置 </el-button>
        <el-button type="primary" @click="addHandle">新增 </el-button>
      </el-form-item>
    </el-form>
    <el-table :data="bookinfolist" style="width: 100%" max-height="100%">
      <el-table-column
        fixed
        type="index"
        label="序号 "
        width="60"
      ></el-table-column>
      <el-table-column
        prop="bookISBN"
        label="书号 "
        width="150"
      ></el-table-column>
      <el-table-column
        prop="bookName"
        label="书名 "
        width="200"
      ></el-table-column>
      <el-table-column
        prop="author"
        label="作者 "
        width="100"
      ></el-table-column>
      <el-table-column
        prop="press"
        label="出版社 "
        width="150"
      ></el-table-column>
      <el-table-column
        prop="publicationdate"
        label="出版日期 "
        width="150"
      ></el-table-column>
      <el-table-column prop="price" label="价格 " width="100"></el-table-column>
      <el-table-column
        prop="quantity"
        label="数量 "
        width="100"
      ></el-table-column>
      <el-table-column prop="bookType" label="图书类型 " width="100">
        <template slot-scope="scope">
          <span>{{ scope.row.bookType | bookTypeFilter }}</span>
        </template>
      </el-table-column>
      <el-table-column fixed="right" label="操作" width="150">
        <template slot-scope="scope">
          <el-button
            size="mini"
            type="primary"
            @click="handleEdit(scope.row.id)"
            >编辑</el-button
          >
          <el-button
            size="mini"
            type="danger"
            @click="handleDelete(scope.row.id)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage"
      :page-sizes="[10, 20, 30, 40]"
      :page-size="pageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="totl"
    >
    </el-pagination>

   <el-dialog title="编辑图书信息" :visible.sync="dialogFormVisible" width="500px">
      <el-form :model="form" label-width="100px" label-position="right" ref="addForm" style="width:400px;margin-top:-20px; " :rules="rules">
        <el-form-item label="书号" prop="bookISBN">
          <el-input v-model="form.bookISBN" size="mini"></el-input>
        </el-form-item>
        <el-form-item label="书名" prop="bookName">
          <el-input v-model="form.bookName" size="mini" type="textarea"></el-input>
        </el-form-item>
        <el-form-item label="作者" prop="author">
          <el-input v-model="form.author" size="mini"></el-input>
        </el-form-item>
        <el-form-item label="出版社" prop="press">
          <el-input v-model="form.press" size="mini"></el-input>
        </el-form-item>
        <el-form-item label="出版日期" prop="publicationdate" >
          <el-date-picker
            size="mini"
            v-model="form.publicationdate"
            align="right"
            type="date"
            placeholder="选择日期"
           
          ></el-date-picker>
        </el-form-item>
        <el-form-item label="价格" prop="price">
          <el-input v-model="form.price" size="mini"></el-input>
        </el-form-item>
        <el-form-item label="数量" prop="quantity">
          <el-input v-model.number="form.quantity" size="mini"></el-input>
        </el-form-item>
        <el-form-item label="图书类型" prop="bookType">
          <el-select v-model="form.bookType" placeholder="请选择图书类型" size="mini">
            <el-option
              v-for="option in bookTypeOptions"
              :key="option.type"
              :label="option.name"
              :value="option.type"
            ></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false" size="mini">取 消</el-button>
        <el-button type="primary" @click="form.id===null?addData('addForm'):updateData('addForm')" size="mini">确 定</el-button>
      </div>
    </el-dialog>
    <el-dialog title="选择出版社" :visible.sync="dialogPressVisible" width="800px">
       <Press @sendData="getData"></Press>
     </el-dialog>
  </div>
</template>
<script>
import bookinfoApi from "@/api/bookinfo";
import Press from "@/views/press/press.vue"
const bookTypeOptions = [
  { type: "1", name: "编程类" },
  { type: "2", name: "前端类" },
  { type: "3", name: "设计类" },
  { type: "4", name: "移动开发类" },
];
export default {
  data() {
    return {
      dialogFormVisible: false,
      dialogPressVisible:false,
      form: {
        id:null,
        bookISBN:'',
        bookName:'',
        author:'',
        press:'',
        publicationdate:'',
        quantity:500,
        price:0,
        bookType:''
      },

      bookinfolist: [],
      total: 0, // 总记录数
      currentPage: 1, // 当前页, 默认第 1 页
      pageSize: 10, // 每页显示条数， 10 条
      searchWhewe: {
        bookName: "",
        press: "",
        bookType: "",
        publicationdate: "",
      }, // 查询条件

      rules: {
        //表单校验规则
        bookISBN: [
          {
            required: true,
            message: "请输入书号 ",
            trigger: "blur",
          },
        ],
        bookName: [
          {
            required: true,
            message: "请输入书名 ",
            trigger: "blur",
          },
        ],
        publicationdate: [
          {
            required: true,
            message: "请选择日期 ",
            trigger: ["blur", "change"],
          },
        ],
        quantity: [
          {
            required: true,
            message: "数量不能 为空",
            trigger: "blur",
          },
          {
            type: "number",
            message: "数量必须为数字值",
            trigger: "blur",
          },
        ],
      },

      bookTypeOptions, //等价 bookTypeOptions:bookTypeOptions /* bookTypeOptions:[ { type:'1',name:'编程类'}, { type:'2',name:'前端类'}, { type:'3',name:'设计类'}, { type:'1',name:'移动开发类'} ] */
      pickerOptions: {
        shortcuts: [
          {
            text: "最近一周",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "最近一个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "最近三个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit("pick", [start, end]);
            },
          },
        ],
      },
    };
  },
  filters: {
    bookTypeFilter(type) {
      const booktypeobj = bookTypeOptions.find((obj) => obj.type === type);
      return booktypeobj ? booktypeobj.name : null;
    },
  },
  created() {
    this.fetchData();
  },
  components:{
    Press },

  methods: {
    
    fetchData() {
      //bookinfoApi.getBookInofList().then((response) => {
      bookinfoApi
        .search(this.currentPage, this.pageSize, this.searchWhewe)
        .then((response) => {
          const resp = response.data;
          this.bookinfolist = resp.data.rows;
          //console.log(resp);
          this.total = resp.data.total;
        });
    },

    //弹出添加数据并重置表单数据和校验结果
    addHandle() {
      this.dialogFormVisible = true;
      this.$refs["addForm"].resetFields();
     
    },

    addData(formName){
      this.$refs[formName].validate((valid) => {
          if (valid) {
            // 
            console.log('add')
            bookinfoApi.add(this.form).then(response=>{
              const resp=response.data
              if(resp.flag)
              {
                this.fetchData()
                this.dialogFormVisible=false
              }
              else{
                this.$message({
                  message:resp.message,
                  type:'warning'
                })
              }
            })
          } else {
            console.log('error submit!!');
            return false;
          }
        });
    },

    handleEdit(id) {
      console.log("编辑" + id);
      this.addHandle();
      bookinfoApi.getBookById(id).then((response) => {
        const resp = response.data;
        if (resp.flag) {
          this.form = resp.data;
        } else {
          this.$message({ message: resp.message, type: "warning" });
        }
      });
    },
  
  
//更新方法 
 updateData(formName){
      console.log('update')
       this.$refs[formName].validate((valid) => {
          if (valid) {
            bookinfoApi.updateBook(this.form).then(response=>{
              const resp=response.data
              if(resp.flag)
              {
                this.fetchData();
                this.form.id=null
                this.dialogFormVisible=false
              }
              else{
                this.$message({
                  message:resp.message,
                  type:'warning'
                })
              }
            })
          }
          else
          {
            return false
          }
       })
    },
//删除
     handleDelete(id) {
     
      this.$confirm('确定要删除吗?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
           console.log('delete');
           bookinfoApi.deleteBookById(id).then(response=>{
             const resp=response.data
              this.$message({
                type: resp.flag?'success':'error',
                message: resp.message              
          });
           if(resp.flag)
           {
             this.fetchData()
           }
           })          
        }).catch(() => {
            console.log('cancel');
           
        });
  
    },
   getData(currentRow){
     // console.log('父组件',currentRow)
     this.searchWhewe.press=currentRow.pressName
      this.searchWhewe.id=currentRow
      this.dialogPressVisible=false 
    }
  
  },
  
  resetForm(formName) {
    this.$refs[formName].resetFields();
  },


  handleSizeChange: function (size) {
    //size 接收到的就是调整后的每 页多少条记录
    this.pageSize = size; //console.log(this.pagesize);
    this.fetchData();
  },
  handleCurrentChange: function (currentPage) {
    // currentPage 接收到的就是当前的页码，包括单击 前往 到页码
    this.currentPage = currentPage; //console.log(this.currentPage); //点击第几页
    this.fetchData();
  },
};
</script>

