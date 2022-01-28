<template>
  <button @click="$emit('clicar')" >
    {{this.funcao}}
  </button >
</template>

<script>
export default {
    name: 'Button',
    props: {
      funcao: String
    }
}
</script>

<style scoped>
button{
  border-radius: 15px;
  margin: 10px;
  font-size: 11px;
  min-width: 210px;
}
button:hover{
  transition: 1s;
  background: Aquamarine;
}
</style>