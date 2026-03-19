<template>
  <div class="container">
    <h1>📦 ระบบสินค้าทั่วไป</h1>

    <nav class="nav">
      <button @click="goHome" :class="{ active: page === 'home' }">
        🏠 หน้าหลัก
      </button>
      <button @click="goAdd" :class="{ active: page === 'add' }">
        ➕ เพิ่มสินค้า
      </button>
    </nav>

    <!-- HOME -->
    <div v-if="page === 'home'" class="card">
      <h2>รายการสินค้า</h2>

      <h3>💰 รวมทั้งหมด: {{ totalPrice }} บาท</h3>

      <table>
        <thead>
          <tr>
            <th>ID</th>
            <th>ชื่อสินค้า</th>
            <th>ราคา</th>
            <th>จัดการ</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="p in products" :key="p.id">
            <td>{{ p.id }}</td>
            <td>{{ p.name }}</td>
            <td>{{ p.price }} บาท</td>
            <td>
              <button class="edit" @click="editProduct(p)">แก้ไข</button>
              <button class="delete" @click="deleteProduct(p.id)">ลบ</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- ADD -->
    <div v-if="page === 'add'" class="card">
      <h2>เพิ่มสินค้า</h2>

      <div class="form-group">
        <label>ชื่อสินค้า</label>
        <input v-model="name" placeholder="กรอกชื่อสินค้า" />
      </div>

      <div class="form-group">
        <label>ราคา</label>
        <input v-model="price" type="number" placeholder="กรอกราคา" />
      </div>

      <button class="primary" @click="addProduct">💾 บันทึก</button>
    </div>

    <!-- EDIT -->
    <div v-if="page === 'edit'" class="card">
      <h2>แก้ไขสินค้า</h2>

      <div class="form-group">
        <label>ชื่อสินค้า</label>
        <input v-model="name" />
      </div>

      <div class="form-group">
        <label>ราคา</label>
        <input v-model="price" type="number" />
      </div>

      <button class="primary" @click="updateProduct">✅ อัปเดต</button>
      <button @click="cancelEdit">❌ ยกเลิก</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      page: "home",
      name: "",
      price: "",
      editId: null,
      products: [] // ✅ ห้ามใช้ localStorage ตรงนี้
    };
  },

  mounted() {
    // ✅ ใช้ได้เฉพาะ client
    if (process.client) {
      const data = localStorage.getItem("products");
      this.products = data ? JSON.parse(data) : [];
    }
  },

  computed: {
    totalPrice() {
      return this.products.reduce((sum, p) => sum + Number(p.price), 0);
    }
  },

  watch: {
    products: {
      handler(newVal) {
        if (process.client) {
          localStorage.setItem("products", JSON.stringify(newVal));
        }
      },
      deep: true
    }
  },

  methods: {
    goHome() {
      this.page = "home";
      this.resetForm();
    },

    goAdd() {
      this.page = "add";
      this.resetForm();
    },

    addProduct() {
      if (!this.name.trim() || this.price <= 0) {
        alert("กรุณากรอกข้อมูลให้ถูกต้อง");
        return;
      }

      this.products.push({
        id: Date.now(),
        name: this.name,
        price: Number(this.price)
      });

      this.resetForm();
      this.page = "home";
    },

    deleteProduct(id) {
      if (!confirm("คุณแน่ใจว่าต้องการลบสินค้า?")) return;

      this.products = this.products.filter(p => p.id !== id);
    },

    editProduct(product) {
      this.page = "edit";
      this.editId = product.id;
      this.name = product.name;
      this.price = product.price;
    },

    updateProduct() {
      this.products = this.products.map(p => {
        if (p.id === this.editId) {
          p.name = this.name;
          p.price = Number(this.price);
        }
        return p;
      });

      this.resetForm();
      this.page = "home";
    },

    cancelEdit() {
      this.resetForm();
      this.page = "home";
    },

    resetForm() {
      this.name = "";
      this.price = "";
      this.editId = null;
    }
  }
};
</script>

<style>
body {
  background: #f4f6f9;
}

.container {
  font-family: "Segoe UI", sans-serif;
  max-width: 900px;
  margin: auto;
  padding: 20px;
}

h1 {
  text-align: center;
  color: #333;
}

.nav {
  text-align: center;
  margin-bottom: 20px;
}

.nav button {
  margin: 5px;
  padding: 10px 18px;
  border: none;
  background: #ddd;
  border-radius: 8px;
  cursor: pointer;
  transition: 0.3s;
}

.nav button:hover {
  background: #bbb;
}

.nav .active {
  background: #4caf50;
  color: white;
}

.card {
  background: white;
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 5px 15px rgba(0,0,0,0.1);
  transition: 0.3s;
}

.card:hover {
  transform: translateY(-3px);
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 15px;
}

th {
  background: #4caf50;
  color: white;
}

th, td {
  padding: 12px;
  border-bottom: 1px solid #ddd;
  text-align: center;
}

tr:hover {
  background: #f1f1f1;
}

button {
  padding: 6px 12px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
}

button.edit {
  background: #2196f3;
  color: white;
}

button.delete {
  background: #f44336;
  color: white;
}

button.primary {
  background: #4caf50;
  color: white;
  margin-top: 10px;
}

button:hover {
  opacity: 0.8;
}

.form-group {
  margin-bottom: 10px;
}

input {
  width: 100%;
  padding: 8px;
  border-radius: 6px;
  border: 1px solid #ccc;
}
</style>