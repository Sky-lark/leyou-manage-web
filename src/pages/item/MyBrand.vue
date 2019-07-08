<template>
  <div>
    <v-layout class="px-4 pb-2">
      <v-flex xs2>
        <v-btn color="info">新增</v-btn>
      </v-flex>
      <v-spacer/>
      <v-flex xs4>
        <v-text-field label="搜索" hide-details append-icon="search" v-model="key"></v-text-field>
      </v-flex>
    </v-layout>
    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center">
          <img v-if="props.item.image" :src="props.item.image" width="130" height="40">
        </td>
        <td class="text-xs-center">{{ props.item.letter }}</td>
        <td class="text-xs-center">
          <v-btn color="info" icon>
            <v-icon>edit</v-icon>
          </v-btn>
          <v-btn color="error" icon>
            <v-icon>delete</v-icon>
          </v-btn>
        </td>
      </template>
    </v-data-table>
  </div>
</template>

<script>
  import VSpec from "./specification/Specification";

  export default {
    name: "MyBrand",
    components: {VSpec},
    data() {
      return {
        headers: [
          {text: 'id', align: 'center', sortable: true, value: 'id'},
          {text: '品牌名称', align: 'center', sortable: false, value: 'name'},
          {text: '品牌LOGO', align: 'center', sortable: false, value: 'image'},
          {text: '品牌首字母', align: 'center', sortable: true, value: 'letter'},
          {text: '操作', align: 'center'},
        ],
        brands: [],
        pagination: {},
        totalBrands: 0,
        loading: true,
        key: "",//搜索条件
      }
    },
    created() {
      this.brands = [
        {id: 1, name: "小米", image: '', letter: "m"}
      ];
      this.totalBrands = 15;
      this.loadBrands();
    },
    watch: {
      key() {
        if (this.pagination.page = 1) {
          this.loadBrands();
        }else {
          this.pagination.page = 1;
        }
      },
      pagination: {
        deep: true,
        handler() {
          this.loadBrands();
        }
      }
    }
    ,
    methods: {
      loadBrands() {
        this.$http.get("/item/brand/page", {
          params: {
            key: this.key,            //搜索条件
            page: this.pagination.page,//当前页
            rows: this.pagination.rowsPerPage,//每页大小
            sortBy: this.pagination.sortBy,//排序字段
            desc: this.pagination.descending,//是否降序
          }
        }).then(resp => {
          this.brands = resp.data.items;
          this.totalBrands = resp.data.total;
        })
      }
    }
  }
</script>

<style scoped>

</style>
