<template>
  <q-page padding>
    <q-table
      title="Meus lugares"
      :rows="lugares"
      :columns="columns"
      row-key="uid"
      :loading="loading"
    >
      <template v-slot:top-right>
        <q-btn label="Novo" icon="add" color="primary" :to="{ name: 'form' }" />
      </template>

      <template v-slot:body-cell-acoes="props">
        <q-td>
          <q-btn
            color="negative"
            icon="delete"
            class="float-right"
            dense
            @click="handleDelete(props.row.uid)"
          />
        </q-td>
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import { api } from 'boot/axios'

const columns = [
  {
    name: 'uid',
    label: 'Id',
    align: 'left',
    field: row => row.uid,
    sortable: true
  },
  {
    name: 'descricao',
    label: 'Descrição',
    align: 'left',
    field: row => row.descricao,
    sortable: true
  },
  {
    name: 'latitude',
    label: 'Latitude',
    align: 'left',
    field: row => row.latitude,
    sortable: true
  },
  {
    name: 'longitude',
    label: 'Longitude',
    align: 'left',
    field: row => row.longitude,
    sortable: true
  },
  {
    name: 'acoes',
    label: 'Ações',
    align: 'rigth',
    field: row => row.acoes
  }
]

export default defineComponent({
  name: 'IndexPage',

  setup () {
    const lugares = ref([])
    const loading = ref(true)

    onMounted(() => {
      handleGetList()
    })

    const handleGetList = async () => {
      try {
        loading.value = true
        const { data } = await api.get('lugares')
        lugares.value = data.data.map((lugar) => {
          return {
            uid: lugar.uid,
            ...lugar.attributes
          }
        })
        loading.value = false
      } catch (error) {
        console.error(error)
        loading.value = false
      }
    }

    const handleDelete = async (uid) => {
      try {
        await api.delete(`lugares/${uid}`)
        handleGetList()
      } catch (error) {
        console.error(error)
      }
    }

    return {
      columns,
      lugares,
      loading,
      handleDelete
    }
  }
})
</script>
