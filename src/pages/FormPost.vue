<template>
    <q-page padding>
    <q-form
      @submit="onSubmit"
      class="row q-col-gutter-sm"
    >
      <q-input
        outlined
        filled
        v-model="form.title"
        label="Título"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => !!val || 'Campo obrigatório' ]"
      />
      <q-input
        outlined
        filled
        v-model="form.author"
        label="Autor"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[ val => !!val || 'Campo obrigatório' ]"
      />
      <div class="col-lg-12 col-xs-12">
        <q-editor v-model="form.content" 
        min-height="Srem" 
        :rules="[ val => !!val || 'Campo obrigatório' ]"/>
      </div>

      <div class="col-12">
        <q-btn 
        label="Salvar" 
        type="submit" 
        color="primary"
        />
        <q-btn label="Cancelar" 
        type="reset" 
        color="negative" 
        class="q-ml-sm" 
        :to="{ name: 'home' }"
        />
      </div>

    </q-form>
    </q-page>

</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import { useQuasar } from 'quasar'
import postService from 'src/services/posts'
import { useRouter, useRoute } from 'vue-router'
const url = import.meta.env.VITE_API_URL

export default defineComponent({
  name: 'FormPost',
  setup () {
    const { post, getById, update } = postService()
    const $q = useQuasar()
    const router = useRouter()
    const route = useRoute()
    const form = ref({
        title: '',
        content: '',
        author: ''
    })

    onMounted( async () => {
      if (route.params.id) {
        getPost(route.params.id)
      }
      
    })


    const getPost = async (id) => {
      try {
        const response = await getById(id)
        form.value = response
      } catch (error) {
        console.error(error)
      }
    }

    const onSubmit = async () => {
      try {
        if (form.value.id) {
          await update(form.value)
        } else {
          await post(form.value)
        }
        console.log(`${url}/posts` ,form.value) 
        await post(form.value)
        $q.notify({message: 'Adicionado com sucesso', icon: 'check', color: 'green'})
        router.push({ name: 'home' })
      } catch (error) {
        console.error(error)
      }
     
    }
    return {
        form,
        onSubmit,
        onMounted,
        getPost
    }
  }
})

</script>