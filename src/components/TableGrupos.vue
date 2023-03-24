<template>
    <table class="animate__animated animate__fadeIn" :id="idTable" role="grid" v-if="(groups.length && tipoGrupo === tgPorLasCasas)">
        <thead>
            <tr>
                <th scope="col"><b>Grupo 1</b></th>
                <th scope="col"><b>Grupo 2</b></th>
                <th scope="col"><b>Grupo 3</b></th>
                <th scope="col"><b>Grupo 4</b></th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(group, index) in groups" :key="index">
                <td v-for="(person, idx) in group" :key="idx">{{ person.name }}</td>
            </tr>
        </tbody>
    </table>
    <table class="animate__animated animate__fadeIn grupoPreparacion" :id="idTable" role="grid" v-else-if="(groups.length && tipoGrupo === tgPreparacion)">
      <thead>
      <tr>
        <th><b>Grupo</b></th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(group, index)  in groupsPreparacion" :key="index">
        <td v-for="(person, idx) in group" :key="idx">{{ person.name }}</td>
      </tr>
      </tbody>
    </table>
    <p v-else>Aun no se han generado grupos.</p>
</template>

<script>

export default {
    name: 'TableGrupos',
    props: ['groups', 'tipoGrupo', 'idTable'],

    data () {
        return {
          tgPorLasCasas: '1',
          tgPreparacion: '2',
        }
    },
    computed:{
      groupsPreparacion: function(){
        let group = [];
            this.groups.forEach((item) => {
              group.push([item.shift()]);
        })
        return group;
      }
    }
}
</script>

<style>
table.grupoPreparacion{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
table > th{
  font-size: small;
}
</style>