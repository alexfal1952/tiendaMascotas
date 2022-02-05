<template>
  <div>
    <b-navbar toggleable='sm' type="dark" variant="info" class="miEstiloNav">
      <b-navbar-brand href="#">
        <img src="https://drive.google.com/uc?id=16jnE-VmKXs41srUz1uuPeb4ljbXTG2tx" alt="" width="50px">
      </b-navbar-brand>
      <b-navbar-toggle target="nav-collapse">
      </b-navbar-toggle>
     <!--  <b-collapse id="nav-collapse" is-nav class="" > -->
      <b-collapse id="nav-collapse" is-nav class="" style="justify-content: flex-end;">
        <b-navbar-nav class="ml-auto">
          <b-nav-form style="    display: flex;align-items: center;">
            <b-input-group size="sm">
              <b-input-group-prepend is-text>
                <b-icon icon="search"></b-icon>
              </b-input-group-prepend>
              <b-form-input type="search" placeholder="Buscar Productos y categorias">
              </b-form-input>
            </b-input-group>
          </b-nav-form>
          <b-col class="p-2">
            <b-icon icon="handbag"></b-icon>
            <p style="font-size: 10px;">Bolsa:{{cantidadCompra == 0 ? '':cantidadCompra}}</p>
          </b-col>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
  </div>
</template>
<script>
export default {
  name: 'Nav',
  props: {
    cantidadCompra: {
      type: Number,
      default: 0
    },
  }
}
</script>
<style scoped>
  .miEstiloNav{
    padding-left: 10px;
    padding-right: 10px;
  }
</style>