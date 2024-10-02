<script setup>
import {computed, onMounted, reactive, ref} from 'vue'
//Initialize
let books=reactive(JSON.parse(localStorage.getItem('books'))||[]);
const showModalEdit=ref(false);
const showModalDelete=ref(false);
let typeSubmit='add';
const book=reactive({
  id:'',
  nameBook:'',
  nameUser:'',
  dateBorrow:'',
  dateReturn:'',
  status:false,
})
//function resetBook
const resetBook=()=>{
  book.id = '',
  book.nameBook = '',
  book.nameUser = '',
  book.dateBorrow = '',
  book.dateReturn = '',
  book.status = false
}
//click button Add New Book
const handleFormEdit=()=>{
  showModalEdit.value=true;
}
//click button Edit Book
const editBook=(id)=>{
    showModalEdit.value=true;
    typeSubmit='edit';
    const bookEdit=books.find(u=>u.id===id);
    if(bookEdit){
        book.id = bookEdit.id;
        book.nameBook = bookEdit.nameBook;
        book.nameUser = bookEdit.nameUser;
        book.dateBorrow = bookEdit.dateBorrow;
        book.dateReturn = bookEdit.dateReturn;
        book.status = bookEdit.status;
    }   
}
//close modal edit
const closeModalEdit=()=>{
  showModalEdit.value=false;
}
// add new Book
const addbook=()=>{
  if (typeSubmit=='add'){
    book.id=new Date().getTime();
    books.push({...book});
    localStorage.setItem('books',JSON.stringify(books));
    resetBook();
    showModalEdit.value=false;
  }else{
    books=books.map(u=>u.id===book.id? {...book}:u);
    localStorage.setItem('books',JSON.stringify(books));
    resetBook();
    showModalEdit.value=false;
    typeSubmit='add';
  }
}
//delete Book
const deleteBook=(id)=>{
  showModalDelete.value=true;
  book.id=id;
}
const cancelDelete=()=>{
    showModalDelete.value=false;
    book.id='';
}
const confirmDelete=()=>{
    books=books.filter(u=>u.id!==book.id);
    localStorage.setItem('books',JSON.stringify(books));
    showModalDelete.value=false;
    book.id='';
}

//get date now
let date=ref('');
onMounted(()=>{
  date.value=new Date().toISOString().split('T')[0];
  console.log(date.value);
  
})
//change status
const changeStatus=(id,event)=>{
  const newStatus=event.target.value==='true';
  books=books.map(u=>u.id===id? {...u,status:newStatus}:u);
  localStorage.setItem('books',JSON.stringify(books));
}

</script>

<template>
     <body>
      <div class="w-[80%] m-auto mt-4 h-[100vh]">
      <main class="main">
        <header class="d-flex justify-content-between mb-3">
          <h3>Quản lý mượn trả sách</h3>
          <button @click="handleFormEdit" class="btn btn-primary">Thêm mới</button>
        </header>
        <table class="table table-bordered table-hover table-striped">
          <thead>
            <tr>
              <th>STT</th>
              <th>Tên sách</th>
              <th>Tên người mượn</th>
              <th>Ngày mượn</th>
              <th>Ngày trả</th>
              <th>Trạng thái</th>
              <th colspan="2">Chức năng</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(btn,index) in books" :key="btn.id">

              <td>{{ index+1 }}</td>
              <td>{{ btn.nameBook }}</td>
              <td>{{ btn.nameUser }}</td>
              <td>{{ btn.dateBorrow }}</td>
              <td>{{ btn.dateReturn }}</td>
              <td>
                <div style="display: flex; align-items: center; gap: 8px">
                  <select 
                    :style="{ backgroundColor: btn.status ? 'green' : 'red', color: 'white', padding: '5px', borderRadius: '5px', border: 'transparent' }" 
                    v-model="btn.status" 
                    @change="changeStatus(btn.id, $event)"
                  >
                     <option value="true">Đã trả</option>
                     <option value="false">Chưa trả</option>
                  </select>
                </div>
              </td>
              <td>
                <span @click="editBook(btn.id)" class="button button-edit">Sửa</span>
              </td>
              <td><span @click="deleteBook(btn.id)" class="button button-delete">Xóa</span></td>
            </tr>
          </tbody>
        </table>
      </main>
    </div>
     <div class="overlay" v-show="showModalEdit">
      <form class="form" @submit.prevent="addbook">
        <div class="d-flex justify-content-between align-items-center">
          <h4>Chỉnh sửa sách</h4>
          <i @click="closeModalEdit" class="fa-solid fa-xmark"></i>
        </div>
        <div>
          <label class="form-label" for="bookName">Tên sách</label>
          <input id="bookName" type="text" class="form-control" required v-model="book.nameBook"/>

        </div>
        <div>
          <label class="form-label" for="dateOfBirth">Tên người mượn</label>
          <input id="dateOfBirth" type="text" class="form-control" required v-model="book.nameUser" />
        </div>
       
        <div>
          <label class="form-label" for="email">Ngày mượn </label>
          <input :min="date" id="email" type="date" class="form-control" required v-model="book.dateBorrow" />
        </div>
      
        <div>
          <label class="form-label" for="address">Ngày trả</label>
          <input :min="book.dateBorrow" type="date" class="form-control" required v-model="book.dateReturn"/>
        </div>
        <div>
          <button type="submit" class="w-100 btn btn-primary">{{typeSubmit=='add'?'Thêm mới':'Chỉnh sửa'}}</button>
        </div>
      </form>
    </div>

   
    

    <!-- Modal xác nhận xóa tài khoản -->
    <div class="overlay" v-if="showModalDelete">
      <div class="modal-custom">
        <div class="modal-title">
          <h4>Cảnh báo</h4>
          <i class="fa-solid fa-xmark"></i>
        </div>
        <div class="modal-body-custom">
          <span>Bạn có chắc chắn muốn xóa tài khoản này?</span>
        </div>
        <div class="modal-footer-custom">
          <button @click="cancelDelete" class="btn btn-light">Hủy</button>
          <button @click="confirmDelete" class="btn btn-danger">Xác nhận</button>
        </div>
      </div>
    </div>
  </body>
</template>

<style scoped>
 
</style>
