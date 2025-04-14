<template>
  <q-page padding>
    <q-input outlined v-model="text" label="Pesquise aqui" /> <br/>
    <q-table
      title="Artigos"
      :rows="posts"
      :columns="columns"
      row-key="id"
    >
    <template v-slot:top>
      <span class="text-h5">Artigos</span>
      <q-space />
      <q-btn color="primary" label="Novo" :to="{ name: 'formPost'}" @click="addArtigo"/>
    </template>
    <template v-slot:body-cell-actions="props">
      <q-td :props="props">
        <q-btn icon="edit" color="info" dense size="sm" @click="handleEditPost(props.row.id)"/>
        <q-btn icon="delete" color="negative" dense size="sm" @click="handleDeletePost(props.row.id)"/>
      </q-td>
    </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue';
import postService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'
const url = import.meta.env.VITE_API_URL

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])

    const { list, remove } = postService()
    const columns = ref([
      { name: 'id', label: 'id', field: 'id', sortable: true, align: 'left'},
      { name: 'title', label: 'title', field: 'title', sortable: true, align: 'left' },
      { name: 'author', label: 'author', field: 'author', sortable: true, align: 'left' },
      { name:'actions', field: 'actions', label: 'Ações', align: 'right' }
    ])
    const $q = useQuasar()
    const router = useRouter()

    onMounted(() => {
      getPosts()
    })

    const getPosts = async () => {
      try {
        const data = await list(`${url}/posts`)
        posts.value = data
      } catch (error) {
        console.error(error)
      }
    }

    const handleDeletePost = async (id) => {
      try {
        $q.dialog({
          title: 'Atenção',
          message: 'Você tem certeza que deseja apagar?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(`${url}/posts`, id)
        $q.notify({message: 'Apagado com sucesso', icon: 'check', color: 'green'})
        await getPosts()
        })
      } catch (error) {
        console.error(error)
        $q.notify({message: 'Erro ao apagar.', icon: 'times', color: 'red'})
      }
    }

    const handleEditPost = (id) => {
      router.push({ name: 'formPost', params: { id } })
    }

    return {
      columns,
      posts,
      handleDeletePost,
      handleEditPost
    }
  }
})
</script>
