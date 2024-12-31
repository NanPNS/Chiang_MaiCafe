<script setup>
// import . . .
import { ref, computed } from "vue";

// ตัวแปร
const travelList = ref([
  { name: 'Forest_Bake', price: 60, img: "https://dynamic-media-cdn.tripadvisor.com/media/photo-o/15/1a/07/54/welcome-to-forest-bake.jpg?w=800&h=500&s=1" },
  { name: 'Naturetalk', price: 60, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR21BNoSzL21Zt7jpRCy1LqW6UGX1lnsBimTg&s" },
  { name: '123_Glamp', price: 80, img: "https://img.wongnai.com/p/1920x0/2021/07/07/280e88d91599487ba93109373928047a.jpg" },
  { name: 'Magokoro', price: 80, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS5uo3RTteOFJbjjg1_gaBJshKujUyQSNx27A&s" },
  { name: 'Lamour_cafe', price: 40, img: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRAl16cwrNe6G6fNqnbE5xOmEXOVbGuiU-83Q&s" },
  { name: 'Yelloo', price: 80, img: "https://pbs.twimg.com/media/EP5jQq3UYAEC4I1.jpg:large" }
]);

const bookingList = ref([]);
const form_costumer = ref({
  name: "",
  phone: "",
  date: "",
  time: ""
});
const showPopup = ref(false);

const totalCost = computed(() => {
  return bookingList.value.reduce((acc, item) => acc + item.price, 0);
});

// Functions
function list_local_control(name, quantity) {
  const existingItemIndex = bookingList.value.findIndex((item) => item.name === name);

  if (existingItemIndex !== -1) {
    bookingList.value[existingItemIndex].quantity += quantity;
    bookingList.value[existingItemIndex].price =
      bookingList.value[existingItemIndex].quantity *
      travelList.value.find((item) => item.name === name).price;
  } else {
    const item = {
      name: name,
      quantity: quantity,
      price: quantity * travelList.value.find((item) => item.name === name).price,
    };
    bookingList.value.push(item);
  }
}

function incrementQuantity(item) {
  item.quantity++;
  item.price = item.quantity * travelList.value.find((travel) => travel.name === item.name).price;
}

function decrementQuantity(item) {
  if (item.quantity > 0) {
    item.quantity--;
    item.price = item.quantity * travelList.value.find((travel) => travel.name === item.name).price;
  }
}

function removeItem(index) {
  bookingList.value.splice(index, 1);
}

function togglePopup() {
  showPopup.value = !showPopup.value;

  const popupElement = document.querySelector('.popup');
  const popupContent = document.querySelector('.popup-content');
  if (showPopup.value) {
    popupElement.classList.add('show');
    popupContent.classList.add('show');
  } else {
    popupElement.classList.remove('show');
    popupContent.classList.remove('show');
  }
}

function showAndAutoClosePopup() {
  showPopup.value = true;

  const popupElement = document.querySelector('.popup');
  const popupContent = document.querySelector('.popup-content');
  popupElement.classList.add('show');
  popupContent.classList.add('show');

  setTimeout(() => {
    showPopup.value = false;

    popupElement.classList.remove('show');
    popupContent.classList.remove('show');
  }, 3000);
}
</script>




<template>
  <div class="main-container">
    <!-- Header Section -->
    <header class="header">
      <h1>Cafe in Chiang Mai</h1>
    </header>

    <!-- Café List Section -->
    <section class="cafe-list">
      <div class="cafe-grid">
        <div class="cafe-card" v-for="(cafe, index) in travelList" :key="index">
          <img :src="cafe.img" alt="Cafe Image" class="cafe-img" />
          <div class="cafe-details">
            <h3>{{ cafe.name }}</h3>
            <p>ราคา: {{ cafe.price }} บาท</p>
            <div class="quantity-input">
              <label>จำนวนโต๊ะ:</label>
              <input type="number" v-model.number="cafe.quantity" placeholder="กรอกจำนวน" />
            </div>
            <button class="btn btn-primary" @click="list_local_control(cafe.name, cafe.quantity)">
              จองโต๊ะ
            </button>
          </div>
        </div>
      </div>
    </section>

    <!-- Main Content -->
    <div class="main-content">
      <!-- Customer Information Section -->
      <div class="customer-info">
        <h2>ข้อมูลลูกค้า</h2>
        <form>
          <div class="form-group">
            <label>ชื่อ-นามสกุล:</label>
            <input type="text" v-model="form_costumer.name" placeholder="กรอกชื่อ-นามสกุล" />
          </div>
          <div class="form-group">
            <label>เบอร์โทร:</label>
            <input type="tel" v-model="form_costumer.phone" placeholder="กรอกเบอร์โทร" />
          </div>
          <div class="form-group">
            <label>วันที่:</label>
            <input type="date" v-model="form_costumer.date" />
          </div>
          <div class="form-group">
            <label>เวลา:</label>
            <input type="time" v-model="form_costumer.time" />
          </div>
          <button class="btn btn-success" @click.prevent="showAndAutoClosePopup">ยืนยันการจอง</button>
        </form>
      </div>

      <!-- Booking List Section -->
      <div class="booking-list">
        <h2>รายการจองโต๊ะ</h2>
        <div class="booking-card-container">
          <div class="booking-card" v-for="(booking, index) in bookingList" :key="index">
            <div class="card-content">
              <div class="card-details">
                <h3>{{ booking.name }}</h3>
                <p>
                  จำนวนโต๊ะ:
                  <button
                    class="btn-quantity"
                    @click="decrementQuantity(booking)"
                    :disabled="booking.quantity === 0"
                  >
                    -
                  </button>
                  <span>{{ booking.quantity }}</span>
                  <button class="btn-quantity" @click="incrementQuantity(booking)">+</button>
                </p>
                <p>ราคา: {{ booking.price }} บาท</p>
              </div>
              <div class="card-actions">
                <button class="btn btn-danger" @click="removeItem(index)">ลบ</button>
              </div>
            </div>
          </div>
        </div>
        <div class="total-cost">
          <h3>รวมราคาทั้งหมด: {{ totalCost }} บาท</h3>
        </div>
      </div>
    </div>
  </div>
  <!-- Popup Section -->
  <div v-if="showPopup" class="popup">
      <div class="popup-content">
        <h2>รายละเอียดการจอง</h2>
        <p><strong>ชื่อ:</strong> {{ form_costumer.name }}</p>
        <p><strong>เบอร์โทร:</strong> {{ form_costumer.phone }}</p>
        <p><strong>วันที่:</strong> {{ form_costumer.date }}</p>
        <p><strong>เวลา:</strong> {{ form_costumer.time }}</p>
        <h3>รายการจอง:</h3>
        <ul>
          <li v-for="(item, index) in bookingList" :key="index">
            {{ index + 1 }}. {{ item.name }} - {{ item.quantity }} โต๊ะ ({{ item.price }} บาท)
          </li>
        </ul>
        <h3>รวม: {{ totalCost }} บาท</h3>
        <button class="btn btn-success" @click="togglePopup">ปิด</button>
      </div>
    </div>
  
</template>



<style>
.booking-card:hover {
  transform: scale(1.02); /* ขยายเล็กน้อยเมื่อ Hover */
  box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
}
.booking-card {
  background: #f9f9f9;
  border-radius: 10px;
  padding: 15px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
.booking-card-container {
  display: flex;
  flex-direction: column;
  gap: 20px;
  margin-top: 20px;
}
body {
  font-family: 'Poppins', sans-serif;
  margin: 0;
  padding: 0;
  background: linear-gradient(to bottom, #4a4f10, #062418);
  color: #333;
}

body::after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.2); /* สีขาวโปร่งใส */
  z-index: -1;
}
button {
  font-size: 0.9rem;
  padding: 8px 12px;
  background: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 10px;
}

.header {
  background: linear-gradient(to right, #5a3c3c, #814d4d); /* Gradient background for header */
  color: white;
  text-align: center;
  padding: 20px;
  font-size: 2rem;
}
.cafe-list {
  padding: 20px;
}
.cafe-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* 3 columns */
  gap: 20px;
  padding: 20px;
  max-width: 1200px; /* Center content */
  margin: auto;
}

.cafe-card {
  background: white;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 100%; /* Auto width */
  overflow: hidden;
  transition: transform 0.3s, box-shadow 0.3s;
  text-align: center;
}

.cafe-img {
  width: 100%;
  height: 150px; /* Reduce image height */
  object-fit: cover; /* Maintain image proportions */
}

.cafe-details {
  padding: 10px; /* Reduce padding for details */
}

.quantity-input input {
  width: 100%; /* Make the input box fit the card */
  padding: 5px; /* Smaller padding */
  font-size: 0.9rem; /* Smaller font size */
}

button {
  font-size: 0.9rem; /* Smaller button text */
  padding: 8px; /* Reduce button padding */
  background: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background: #0056b3;
}

@media (max-width: 1024px) {
  .cafe-grid {
    grid-template-columns: repeat(2, 1fr); /* 2 cards per row on medium screens */
  }
}

@media (max-width: 768px) {
  .cafe-grid {
    grid-template-columns: 1fr; /* 1 card per row on small screens */
  }

  .cafe-card {
    width: 100%; /* Full width on small screens */
  }
}
.cafe-card:hover {
  transform: scale(1.05);
  box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
}

.cafe-img {
  width: 100%;
  height: 150px;
  object-fit: cover;
}

.cafe-details {
  padding: 15px;
}

.cafe-section {
  background: linear-gradient(to bottom right, #4a4f10, #062418); /* Gradient for cafe list */
  padding: 20px 0;
}


.booking-list {
  width: 90%;
  margin: auto;
}


.table {
  margin: auto;
  border-collapse: collapse;
  width: 90%;
}

.table th,
.table td {
  border: 1px solid #ddd;
  padding: 10px;
  text-align: center;
}

.table th {
  background-color: #412626;
  color: white;
}

.popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6); /* พื้นหลังแบบโปร่งแสง */
  display: flex;
  justify-content: center;
  align-items: center;
  opacity: 0; /* ซ่อนเริ่มต้น */
  visibility: hidden; /* ซ่อนเพื่อไม่ให้กดได้ */
  transition: opacity 0.3s ease, visibility 0.3s ease; /* Animation */
}

.popup.show {
  opacity: 1; /* แสดง */
  visibility: visible; /* แสดง */
}

.popup-content {
  background: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  transform: scale(0.9); /* ขนาดเริ่มต้นเล็กลง */
  transition: transform 0.3s ease; /* Animation */
}

.popup-content.show {
  transform: scale(1); /* ขยายขนาดกลับ */
}

button:hover {
  background: #0056b3;
}
.btn {
  padding: 10px 15px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn-close {
  padding: 10px 15px;
  background: #dc3545;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 20px;
  transition: background-color 0.3s ease;
}
.btn-quantity {
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  cursor: pointer;
}
.btn-quantity:hover {
  background: #0056b3;
}

.btn-primary {
  display: block;
  width: 100%;
  padding: 10px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
.btn-primary:hover {
  background-color: #0056b3;
}
.btn-danger {
  padding: 8px 12px;
  border: none;
  border-radius: 5px;
  background-color: #dc3545;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
.btn-danger:hover {
  background: #a71d2a;
}

.btn-success {
  width: 100%;
  padding: 10px;
  background-color: #28a745;
  color: white;
  font-size: 1rem;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.btn-success:hover {
  background-color: #218838;
}
.btn-close:hover {
  background: #a71d2a;
}
.btn-close {
  background: #6c757d;
  color: white;
}
.main-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  padding: 20px;
}
.customer-info {
  width: 50%;
  margin: 20px auto;
  background: #f9f9f9;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
.customer-info,
.booking-list {
  background: white;
  border-radius: 10px;
  padding: 20px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
.customer-info h2 {
  text-align: center;
  margin-bottom: 20px;
  font-family: 'Poppins', sans-serif;
}

.form-group {
  margin-bottom: 15px;
}
.booking-page {
  display: grid;
  grid-template-columns: 1fr 1fr; /* สองคอลัมน์ */
  gap: 20px;
  width: 90%;
  margin: auto;
  padding: 20px;
}
.customer-details,
.booking-summary {
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  padding: 20px;
}
.customer-details h2,
.booking-summary h2 {
  text-align: center;
  margin-bottom: 20px;
  color: #333;
}
.form-group label {
  display: block;
  font-weight: bold;
  margin-bottom: 5px;
}

.form-group input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
}
.booking-card-container {
  display: flex;
  flex-direction: column;
  gap: 15px;
}
.card-content {
  display: flex;
  justify-content: space-between;
  width: 100%;
}
.card-details {
  flex: 1;
}

.card-details h3 {
  font-size: 1.2rem;
  margin-bottom: 5px;
  color: #555;
}

.card-details p {
  font-size: 1rem;
  margin: 5px 0;
  color: #777;
}

.card-actions {
  display: flex;
  align-items: center;
  gap: 10px;
}
.total-cost {
  text-align: center;
  margin-top: 20px;
  font-size: 1.5rem;
  font-weight: bold;
  color: #333;
}

.booking-list h2 {
  text-align: center;
  color: white; /* เปลี่ยนหัวข้อเป็นสีขาว */
  margin-bottom: 20px;
}
input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
  box-sizing: border-box;
}
.quantity-input input {
  width: 100%;
  padding: 5px;
  margin-top: 5px;
}

.footer {
  background: #333;
  color: white;
  text-align: center;
  padding: 10px;
  margin-top: 20px;
}
</style>
