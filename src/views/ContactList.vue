<template>
  <div class="contact-list">
    <!-- 联系人列表 -->
    <van-contact-list
      :list="list"
      @add="onAdd"
      @edit="onEdit"
    />

    <!-- 编辑联系人 -->
    <van-popup v-model="showEdit" position="bottom">
      <van-contact-edit
        :contact-info="editingContact"
        :is-edit="isEdit"
        @save="onSave"
        @delete="onDelete"
      />
    </van-popup>
  </div>
</template>

<script>
import axios from 'axios';
import {ContactList, Toast, ContactEdit, Popup} from 'vant';

export default {
  name: 'contactList',
  components: {
    [ContactList.name]: ContactList,
    [ContactEdit.name]: ContactEdit,
    [Popup.name]: Popup,
  },
  data() {
    return {
      list: [], // 联系人列表
      instance: null, // axios实例
      showEdit: false, // 控制弹出层显示
      editingContact: {}, // 正在编辑的联系人数据
      isEdit: false, // 新建/编辑
    };
  },
  methods: {
    // 获取联系人列表
    async getContactList() {
      const res = await this.$Http.getContactList();
      this.list = res.data;
    },
    // 添加联系人
    onAdd() {
      this.showEdit = true;
      this.isEdit = false;
    },
    // 编辑联系人
    onEdit(info) {
      this.showEdit = true;
      this.isEdit = true;
      this.editingContact = info;
    },
    // 保存联系人
    async onSave(info) {
      if (this.isEdit) {
        // 编辑保存
        const res = await this.$Http.editContact(info);
        if (res.code === 200) {
          Toast('保存成功');
          this.showEdit = false;
          this.getContactList();
        }
      } else {
        // 新建保存
        const res = await this.$Http.newContactForm(info, true, {
          timeout: 200
        });
        if (res.code === 200) {
          Toast('保存成功!');
          this.showEdit = false;
          this.getContactList();
        }
      }
    },
    // 删除联系人
    async onDelete(info) {
      const res = await this.$Http.delContact({
        id: info.id
      });
      if (res.code === 200) {
        Toast('删除成功!');
        this.showEdit = false;
        this.getContactList();
      }
    }
  },
  created() {
    this.instance = axios.create({
      baseURL: 'http://localhost:9000/api/',
      timeout: 1000
    });

    this.getContactList();
  }
}
</script>
