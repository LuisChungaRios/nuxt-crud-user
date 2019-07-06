<template>
  <section >
    <a-row>
      <a-col :xl="24" style="margin: 20px 0">
        <h1 style="text-align: center">CRUD LUMEN - NUXT  | UI  ANT DESIGN</h1>
      </a-col>

    </a-row>
    <a-row>
      <a-col :xl="12">
        <a-table :columns="columns"
                 :bordered="true"
                 :rowKey="record => record.id"
                 :dataSource="data"
                 :pagination="pagination"
                 :loading="loading"

                 @change="handleTableChange"
        >
          <template slot="name" slot-scope="name">

          </template>
        </a-table>
      </a-col>
      <a-col :xl="12">
       <a-form
               :form="formUser"
              id="formUser"
              @submit="myForm"
       >
         <a-form-item
                 label="Nombre: "
                  :label-col="{span:4 }">
                  <a-input placeholder="Ingrese nombre" id="name" style="width: 40%"></a-input>

         </a-form-item>
         <a-form-item
                 label="Email: "
                 :label-col="{span:4 }">
           <a-input placeholder="Ingrese email" style="width: 40%" id="email" ></a-input>

         </a-form-item>
         <a-form-item
                 label="Password: "
                 :label-col="{span:4 }">
           <a-input placeholder="Ingrese password" id="password" type="password" style="width: 40%"></a-input>

         </a-form-item>
         <a-button type="primary"   html-type="submit" style="margin-left: 20%">Registrar</a-button>
       </a-form>
      </a-col>
    </a-row>
  </section>
</template>

<script>
  import axios from 'axios'


  const columns = [{
    title: 'Nombre',
    sorter: true,
    width: '20%',
    dataIndex:'name',
    align: "center"
  }, {
    title: 'Correo',
    width: '20%',
    dataIndex:'email',
    align: "center"
  }
  ];

  export default {
    name: 'index',

    mounted() {
      this.fetch();
    },
    data() {
      return {
        data: [],
        pagination: {},
        loading: false,
        columns,
        formUser: this.$form.createForm(this),
        url : 'http://localhost:8000/api/v1'
      }
    },
    methods: {
      handleTableChange (pagination, filters, sorter) {
        console.log(pagination  )
        const pager = { ...this.pagination };
        pager.current = pagination.current;

        this.fetch({
          results: pagination.pageSize,
          page: pagination.current,
          sortField: sorter.field,
          sortOrder: sorter.order,
          ...filters,
        });
      },
      fetch (params = {}) {
        let me = this

        this.loading = true
        let path = 'http://localhost:8000/api/v1/users'
        axios.get(path)
          .then((response) => {

            const pagination = {...me.pagination}

            pagination.pageSize = 5

            pagination.total = response.data.data.length
            me.data = response.data.data
            me.loading = false
            me.pagination = pagination

          })

      },
      myForm (e) {
        let me = this
        e.preventDefault()
        let data = {
          'name' : e.target.name.value,
          'password': e.target.password.value,
          'email': e.target.email.value
        }
        axios.post(`${this.url}/user`, data)
          .then(response => {
            if (response.data.status == 201) {
              me.$message.success(response.data.message)
              me.formUser.resetFields()
              me.fetch()
            }
          })
      }
    },
  }


</script>

<style>

</style>
