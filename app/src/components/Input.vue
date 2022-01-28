<template>
  <input v-on:keyup="$emit('mudar',this.pesquisa)" v-model="pesquisa" :type="type" :placeholder="placeholder"/>
</template>

<script>
export default {
    name: 'Input',
    props: {
      placeholder: String,
      type: String
    },
    data() {
        return {
            pesquisa: ''
        }
    }
}
</script>

<style scoped>
button{
  border-radius: 15px;
  margin-top: 10px;
}
button:hover{
  transition: 1s;
  background: Aquamarine;
}
input{
  margin: 5px;
}
#submitForm {
  border-radius: 15px;
}
#submitForm:hover{
  transition: 1s;
  background: Aquamarine;
}
</style>