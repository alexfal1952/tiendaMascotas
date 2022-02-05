<template>
  <div>
    <Nav tienda='10' :cantidadCompra="productosCarrito.length" :clickBolsa="verCarrito"></Nav>
    <div class="row">
      <div class="col-12 contenedor">
        <div class="row">
          <div class="col-2 contenedor" style=" background-color:#f6f5f7">
            <div>
              <h4  class="p-3">Categorias</h4>
              <b-nav vertical class="w-25" v-for="(item, index) in productCategory" :key="index">
                <b-nav-item active @click="clickMenu(item)">{{item.name}}</b-nav-item>
              </b-nav>
            </div>
          </div>
          <div class="col-10 ">
            <div>
              <!--  -->
              <b-tabs content-class="mt-3" align="center">
                <b-card-group>
                  <b-card
                    v-for="(producto, indice) in productosFiltrados"
                    :key="indice"
                    :title="producto.name.length > 20 ? producto.name.substring(0,20) + '...' :producto.name"
                    :img-src="producto.photo"
                    img-alt="Image"
                    class=" m1 fotoCard col-3">
                    <b-card-text class="col-12">
                      <b-form-spinbutton  id="demo-sb" v-model="producto.cantidad" min="0" :max="producto.stock" inline>
                      </b-form-spinbutton>
                        <b-button block variant="primary" @click="detalleProducto(producto)">
                          <b-icon icon="cart-plus" ></b-icon>
                        </b-button>
                    </b-card-text>
                  </b-card>
                </b-card-group>
              </b-tabs>
              <!--  -->
            </div>
            <div class="block paginacion" v-if="productosFiltrados.length">
              <el-pagination
                layout="prev, pager, next"
                @current-change="filtradoPorPagina"
                :current-page.sync="paginaActual"
                :page-size="cantidadPorPagina"
                :total="totalPaginacion">
              </el-pagination>
            </div>
            <div class="paginacion" v-else>
              Sin Productos para esta categoria
            </div>
          </div>
        </div>
      </div>
    </div>

    <el-dialog
      :title="productoSeleccionado.nombre"
      :visible.sync="dialogVisible"
      width="40%">

      <b-card
        :title="productoSeleccionado.nombre.length > 50 ? productoSeleccionado.nombre.substring(0,50) + '...' :productoSeleccionado.nombre"
        :img-src="productoSeleccionado.img"
        img-alt="Image"
        class=" m1 col-12">
        ${{productoSeleccionado.precio}}<br>
        Cantodad: {{productoSeleccionado.cantidad}}
      </b-card>

      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">Cancelar</el-button>
        <el-button type="primary" @click="agregarACarrito">Aceptar</el-button>
      </span>
    </el-dialog>

    <el-dialog
      title="Carrito"
      :visible.sync="dialogCarrito"
      width="80%">

      <span>
        <table class="table">
          <thead>
            <tr>
              <th>Producto</th>
              <th>Cantidad</th>
              <th>Precio</th>
              <th>Total</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(producto, indice) in productosCarrito"
              :key="indice">
              <td>{{producto.nombre.length > 50 ? producto.nombre.substring(0,50) + '...' :producto.nombre}}</td>
              <td>{{producto.cantidad}}</td>
              <td>${{producto.precio}}</td>
              <td>${{producto.precio * producto.cantidad}}</td>
            </tr>
          </tbody>
        </table>
      </span>

      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogCarrito = false">Cancelar</el-button>
        <el-button type="primary" >Finalizar Compra</el-button>
      </span>
    </el-dialog>
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
    product: [],
    totalPaginacion: 0,
    cantidadPorPagina: 4,
    paginaActual: 1,
    productosFiltrados: [],
    categoriaSeleccionada: 1,
    dialogVisible: false,
    dialogCarrito: false,
    productoSeleccionado: {
      id: '',
      nombre: '',
      cantidad: 0,
      img: '',
      precio: 0
    },
    productosCarrito: []
  }),
  methods: {
    filtradoPorPagina () {
      const productosPorCategoria = []
      this.product.forEach(producto => {
        producto.cantidad = 0

        let aux = [...this.productosCarrito]
        let enCarrito = aux.find((carrito) => carrito.id === producto.id)
        if (typeof enCarrito !== 'undefined') {
          producto.stock = producto.stock - enCarrito.cantidad
        }

        if (producto.category.id === this.categoriaSeleccionada) {
          productosPorCategoria.push(producto)
        }
      });
      this.totalPaginacion = productosPorCategoria.length

      const productosPorPagina = productosPorCategoria.filter((producto, index) => {
        let rangoHasta = this.paginaActual * this.cantidadPorPagina
        let rangoDesde = rangoHasta - this.cantidadPorPagina
        return index >= rangoDesde && index < rangoHasta
      })
      this.productosFiltrados = productosPorPagina
    },
    clickMenu (item) {
      this.paginaActual = 1
      this.categoriaSeleccionada = item.id
      this.filtradoPorPagina()
    },
    detalleProducto (producto) {
      if (!producto.cantidad) {
        this.$alert('La cantidad debe ser mayor a 0', 'Atención', {
          confirmButtonText: 'OK'
        })
        return
      }
      this.dialogVisible = true

      this.productoSeleccionado.nombre = producto.name
      this.productoSeleccionado.img = producto.photo
      this.productoSeleccionado.id = producto.id
      this.productoSeleccionado.cantidad = producto.cantidad
      this.productoSeleccionado.precio = producto.price
    },
    agregarACarrito () {
      let aux = [...this.productosCarrito]
      let enCarrito = aux.find((carrito) => carrito.id === this.productoSeleccionado.id)
      if (typeof enCarrito !== 'undefined') {
        enCarrito.cantidad += this.productoSeleccionado.cantidad
      } else {
        let auxProducto = {...this.productoSeleccionado}
        this.productosCarrito.push(auxProducto)
      }

      this.filtradoPorPagina()
      this.dialogVisible = false
    },
    verCarrito () {
      this.dialogCarrito = true
    }
  },
  computed: {
  },
  created() {
    axios.get("http://sva.talana.com:8000/api/product-category").then((result) => {
      this.productCategory = result.data
      this.productCategory.sort(function(a, b) {
        return a.order - b
      });
    })
    axios.get("http://sva.talana.com:8000/api/product/").then((result) => {
      this.product = result.data
      this.filtradoPorPagina()
    })
  },
}
</script>
<style>
.fotoCard img{
  max-height: 200px !important;
  /* width: auto; */
}
.fotoCard {
  flex: none !important;
  width: 18% !important;
  display: inline-block;
  margin: auto;
  vertical-align: top !important;
}
.contenedor{
  min-height:calc(100vh - 96px);
}
html{
  overflow-y: scroll;
  overflow-x: hidden;
}
.paginacion {
  text-align: center;
}
</style>
