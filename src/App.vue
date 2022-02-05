<template>
  <div>
    <Nav tienda='10'></Nav>
      <div class="row">
        <div class="col-12 contenedor">
          <div class="col-2 contenedor" style=" background-color:#f6f5f7">
            <div>
              <h4  class="p-3">Categorias</h4>
              <b-nav vertical class="w-25" v-for="(item, index) in productCategory" :key="index">
                <b-nav-item active>{{item.name}}</b-nav-item>
              </b-nav>
            </div>
          </div>
          <div class="col-10 "></div>
      </div>
    </div>
  </div>
</template>

<script>
import Nav from '@/components/Nav.vue'
import axios from "axios";
export default {
  name: 'App',
  components: {
    Nav
  },
  data: () => ({
    productCategory: [],
    product: []
  }),
  created() {
    axios.get("http://sva.talana.com:8000/api/product-category").then((result) => {
      this.productCategory = result.data
      this.productCategory.sort(function(a, b) {
        return a.order - b
      });
    })
    axios.get("http://sva.talana.com:8000/api/product/").then((result) => {
      this.product = result.data
      console.log( this.product)
    })
  },
}
</script>
<style>
.contenedor{
  min-height:calc(100vh - 96px);
}
</style>
