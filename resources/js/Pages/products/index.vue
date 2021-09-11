<template>
  <app-layout>
    <template #header>Products List</template>
    <div class="products-container">
      <div class="add-product">
        <a href="#">
          <p @click="showModal()">
            <span class="material-icons-outlined"> add </span>
          </p>
          <p @click="showModal()">Add product</p>
        </a>
      </div>
      <div class="product-list">
        <div
          class="product-card"
          v-for="product in this.productList"
          v-bind:key="product.id"
        >
          <img src="{{product.image}}" alt="" />
          <h3>{{ product.name }}</h3>
        </div>
      </div>
    </div>
    <div class="modal-container hide">
      <div class="modal-content">
        <form>
          <div class="modal-header">
            <h2>Add product</h2>
            <span @click="closeModal()" class="material-icons-outlined">
              close
            </span>
          </div>
          <div class="modal-body">
            <div class="input-container-left">
              <label>Name *</label>
              <input type="text" v-model="product.name" />
              <p class="hide error-name"></p>
              <label>Description *</label>
              <input type="text" v-model="product.description" />
              <p class="hide error-description"></p>
              <label>price *</label>
              <input type="text" v-model="product.price" />
              <p class="hide error-price"></p>
            </div>
            <div class="input-container-right">
              <div class="product-image">
                <span class="material-icons-outlined">
                  add_photo_alternate
                </span>
                <p>Add Photo</p>
                <input type="file" id="upload-image" />
                <p class="file-name hide"></p>
              </div>
            </div>
          </div>
          <div class="modal-bottom">
            <button id="submit_btn" type="button">Create</button>
          </div>
        </form>
      </div>
    </div>
  </app-layout>
</template>

<script>
import AppLayout from "./../../Layouts/AppLayout.vue";
export default {
  components: {
    AppLayout,
  },
  data() {
    return {
      productList: this.products,
      product: {
        name: null,
        image: null,
        price: null,
        description: null,
      },
    };
  },
  props: ["products"],
  mounted() {
      console.log(this.productList)
    var _this = this;
    $("#upload-image").change(function (e) {
      let fileName = e.target.files[0].name;

      _this.product.image = e.target.files[0];
      $(".file-name").html(fileName);
      $(".file-name").removeClass("hide");
    });

    $("#submit_btn").click(function () {
      var valid = _this.validateForm();
      if (valid) {
        _this.registerProduct();
      }
    });
  },
  methods: {
    showModal() {
      $(".modal-container").removeClass("hide");
    },
    closeModal() {
      $(".modal-container").addClass("hide");
    },
    validateForm() {
      var isOk = false;
      if (this.product.name == null) {
        isOk = false;
        $(".error-name").html("the name field is required");
        $(".error-name").removeClass("hide");
      } else {
        isOk = true;
        $(".error-name").addClass("hide");
      }
      if (this.product.description == null) {
        isOk = false;
        $(".error-description").html("the description field is required");
        $(".error-description").removeClass("hide");
      } else {
        isOk = true;
        $(".error-description").addClass("hide");
      }
      if (this.product.image == null) {
        isOk = false;
        $(".file-name").html("the image field is required");
        $(".file-name").removeClass("hide");
      } else {
        isOk = true;
        $(".file-name").addClass("hide");
      }
      if (this.product.price == null) {
        isOk = false;
        $(".error-price").html("the price field is required");
        $(".error-price").removeClass("hide");
      } else {
        isOk = true;
        $(".error-price").addClass("hide");
      }
      return isOk;
    },
    registerProduct() {
      var _this = this;
      var product = this.product;
      _this.storeImage(product.image);
      axios
        .post("/products/create", {
          product_name: product.name,
          product_description: product.description,
          product_price: product.price,
          product_image_name: product.image.name,
        })
        .then(function (response) {
          console.log(response);
          if (response.data != null) {

          }
        })
        .catch(function (error) {
          console.log(error);
        });
    },
    storeImage() {
         let existingObj = this;

                const config = {
                    headers: {
                        'content-type': 'multipart/form-data'
                    }
                }

                let data = new FormData();
                data.append('file', this.product.image);

                axios.post('/store-image', data, config)
                    .then(function (res) {
                        existingObj.success = res.data.success;
                    })
                    .catch(function (err) {
                        existingObj.output = err;
                    });
    },
  },
};
</script>
<style>
.products-container {
  display: grid;
  grid-template-columns: 30% 80%;
  width: 80%;
  margin: auto;
}
.add-product {
  background-color: gray;
}
.add-product a {
  padding: 100px 0;
  text-align: center;
  display: block;
  color: white;
}
.modal-container {
  position: fixed;
  margin: auto;
  width: 100%;
  height: 100%;
  top: 200px;
}
.modal-content {
  border-radius: 10px;
  box-shadow: 0px 0px 8px 1px rgba(0, 0, 0, 0.3);
  background-color: #fff;
  width: 50%;
  margin: auto;
}
.modal-body {
  padding: 15px;
  display: grid;
  grid-template-columns: 60% 35%;
  grid-gap: 5%;
  align-items: center;
}
.input-container-left {
}
.input-container-left input {
  display: block;
  width: 100%;
}
.input-container-right {
}
.input-container-right div {
  text-align: center;
  border: 2px dashed;
  width: 100%;
  padding: 50px 0;
  cursor: pointer;
}
.modal-header {
  padding: 10px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.3);
  display: flex;
  align-items: center;
}
.modal-header h2 {
  width: 98%;
}
.modal-header span {
  float: right;
  cursor: pointer;
}
.modal-bottom {
  padding: 10px;
  border-top: 1px solid rgba(0, 0, 0, 0.3);
}
.modal-bottom button {
  background-color: green;
  color: #fff;
  padding: 5px 20px;
  border-radius: 5px;
}
.hide {
  display: none;
}
.product-image p {
  position: absolute;
  margin-left: 6%;
}
#upload-image {
  cursor: pointer;
  opacity: 0;
}
</style>
