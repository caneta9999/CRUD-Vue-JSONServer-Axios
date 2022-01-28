<template>
  <tr>
    <td>{{video.nome}}</td>
    <td>{{video.nota}}</td>
    <td>{{video.descricao}}</td>
    <td>
    <button @click="$emit('editar', video)">Editar</button>
    <button @click="$emit('deletar', video)">Deletar</button>
    </td>
  </tr>
</template>

<script>
export default {
    name: 'Tr',
    props: {
      video: Object
    }
}
</script>

<style scoped>
td {
  border: 1px solid black;
  background: white;
  text-align: center;
  word-wrap:break-word;
}
button{
  border-radius: 15px;
  margin: 3px;
}
button:hover{
  transition: 1s;
  background: Aquamarine;
}
</style>