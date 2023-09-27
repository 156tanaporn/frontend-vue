<template>
<div>
<v-data-table
    :headers="headers"
    :items="productItem"
    sort-by="calories"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>จัดการข้อมูล</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-btn
              color="primary"
              dark
              class="mb-2"
              @click="openDialog('add', defaultItem)"
            >
              เพิ่มข้อมูล
            </v-btn>

      </v-toolbar>
    </template>
    <!-- <template v-slot:[`item.role`] = "{ item }">
      {{item.role.name}}
    </template> -->
    <template v-slot:[`item.actions`] = "{ item }">
      <v-btn small outlined @click="openDialog('edit', item)" color="blue">
      <v-icon>
        mdi-pencil
      </v-icon>
      </v-btn>
      <v-btn small outlined @click="deleteItem(item)" color="red" class="ml-2">
      <v-icon>
        mdi-delete
      </v-icon>
      </v-btn>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="primary"
        @click="initialize"
      >
        Reset
      </v-btn>
    </template>
  </v-data-table>
  <v-dialog
          v-model="dialogCreate"
          max-width="500px"
        >
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>

                  <v-col
                    cols="12"
                    sm="6"
                    md="6"
                  >
                    <v-text-field
                      v-model="productName"
                      label="ชื่อสินค้า"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="6"
                  >
                    <v-text-field
                      v-model="productPrice"
                      label="ราคา"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="6"
                  >
                    <v-text-field
                      v-model="productAmout"
                      label="จำนวน"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="6"
                  >
                    <v-text-field
                      v-model="productDetail"
                      label="รายละเอียด"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                ยกเลิก
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save(formTitle)"
              >
                บันทึกข้อมูล
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">cancle</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">ok</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
</div>
</template>

<script>
export default {
  data: () => ({
    productName: '',
    productPrice: '',
    productAmout: '',
    productDetail: '',
    dialogCreate: false,
    dialogDelete: false,
    headers: [
      {
        text: 'ไอดี',
        align: 'start',
        sortable: false,
        value: 'productId'
      },
      { text: 'ชื่อสินค้า', value: 'productName' },
      { text: 'ราคา', value: 'productPrice' },
      { text: 'จำนวน', value: 'productAmout' },
      { text: 'รายละเอียด', value: 'productDetail' },
      { text: 'จัดการ', value: 'actions', sortable: false }
    ],
    productItem: [],
    editedIndex: -1,
    editedItem: {
      name: '',
      calories: 0,
      fat: 0,
      carbs: 0,
      protein: 0
    },
    defaultItem: {
      name: '',
      calories: 0,
      fat: 0,
      carbs: 0,
      protein: 0
    },
    formTitle: '',
    idProduct: '',
    idForDelete: ''
  }),

  watch: {
    dialog (val) {
      val || this.close()
    },
    dialogDelete (val) {
      val || this.closeDelete()
    }
  },

  created () {
    this.initialize()
  },

  methods: {
    async initialize () {
      this.productItem = []
      try {
        var data = await this.axios.get('http://172.28.48.249:9000/product')
        console.log('data product ====>', data)
        this.productItem = data.data
      } catch (error) {

      }
    },
    openDialog (Action, item) {
      this.formTitle = ''
      if (Action === 'add') {
        this.dialogCreate = true
        this.formTitle = 'เพิ่มข้อมูล'
        // this.editedItem = item
      } else {
        this.formTitle = 'แก้ไขข้อมูล'
        this.productName = item.productName
        this.productPrice = item.productPrice
        this.productAmout = item.productAmout
        this.productDetail = item.productDetail
      }
    },

    editItem (item) {
      console.log('item select', item)
      console.log('index item', this.desserts.indexOf(item))
    },

    deleteItem (item) {
      // this.editedIndex = this.desserts.indexOf(item)
      // this.editedItem = Object.assign({}, item)
      this.idForDelete = item.id
      this.dialogDelete = true
    },

    async deleteItemConfirm () {
      try {
        var response = await this.axios.delete('http://172.28.48.249:9000/product', data)
        this.initialize()
      } catch (error) {
        console.log(error.mmessage)
      }
      // this.desserts.splice(this.editedIndex, 1)
      this.closeDelete()
    },

    close () {
      this.dialogCreate = false
      this.productName = ''
      this.productPrice = ''
      this.productAmout = ''
      this.productDetail = ''
    },

    closeDelete () {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    async save (action) {
      var data = {
        productName: this.productName,
        productPrice: this.productPrice,
        productAmout: this.productAmout,
        productDetail: this.productDetail
      }
      if (action === 'เพิ่มข้อมูล') {
        // this.desserts.push(this.editedItem)
        // console.log('data after sent ====>', data)
        try {
          var dataResponse = await this.axios.post('http://172.28.48.249:9000/product', data)
          // console.log('data dataResponse ====>', dataResponse)
          this.close()
          this.initialize()
        } catch (error) {
          console.log(error.message)
        }
      } else {
        try {
          var dataResponseEdit = await this.axios.put('http://172.28.48.249:9000/product' + this.idproduct, data)
          // console.log('data dataResponse ====>', dataResponseEdit)
          this.close()
          this.initialize()
        } catch (error) {
          console.log(error.message)
        }
      }
      this.close()
    }
  }
}
</script>

<style>

</style>
