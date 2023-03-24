<template>

  <h1>{{ nameApp }}</h1>

  <div style="display: flex;align-items: center;" class="animate__animated animate__bounce">
    <textarea class="custom-input" v-model="subTitle" placeholder="Escriba la razón de los grupos..." rows="1" style="margin-bottom: 0 "></textarea>
    <span class="material-icons">mode_edit</span>
  </div><hr/>

  <select id="tipoGrupo" v-model="tipoGrupo" @change="recargarGrupos">
    <option value="" selected>Elija un tipo de grupo</option>
    <option value="1">Celebración por las casas</option>
    <option value="2">Preparación</option>
  </select><hr/>

    <TableGrupos v-if="tipoGrupo.length" :tipoGrupo="tipoGrupo" :idTable="idTable" :groups="groups" :key="componentKey"/>
    <button v-if="tipoGrupo.length" class="animate__animated animate__fadeIn" @click="createGroups">
      {{ buttonMessage }}
      <span v-if="!groups.length" class="material-symbols-outlined material-icons">groups</span>
      <span v-if="groups.length" class="material-icons">autorenew</span>
    </button>

  <div v-if="groups.length" class="comments animate__animated animate__fadeIn">
    <transition-group name="list" tag="ul">
      <li v-for="(item, index) in items" :key="item" class="list-item" style="width: 85%">
        {{ item }}
        <span class="material-icons" style="color: #d83532; cursor: pointer; font-size:20px" @click.prevent="remove(index)">close</span>
      </li>
    </transition-group>

    <textarea ref="comments" v-model="comments" placeholder="Escriba aquí las notas importantes..." style="font-size: medium"> </textarea>
    <a role="button" class="btn-custom outline" href="#" @click.prevent="add">
      <svg xmlns="http://www.w3.org/2000/svg" height="16px" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
        <line x1="12" y1="5" x2="12" y2="19"></line>
        <line x1="5" y1="12" x2="19" y2="12">'</line>
      </svg>
      Agregar
    </a>
  </div>

  <button class="secondary" @click="exportPDF" v-if="groups.length">Descargar <span class="material-icons">picture_as_pdf</span></button>

  <p style="padding-top: 10px; text-align: center; font-size: small;">Desarrollado por la 3° Comunidad de Albacete - {{ year }}</p>
</template>

<script>
import TableGrupos from './TableGrupos.vue';
import _ from 'lodash';
import jsPDF from 'jspdf'
import autoTable from 'jspdf-autotable'

export default {
  components: {
    TableGrupos,

  },
  name: 'GeneradorGrupos',
  data() {
    return {
      componentKey: 0,
      nameApp: 'Generador de grupos',
      names: '',
      tipoGrupo: '',
      title: '',
      subTitle: '',
      comments: '',
      items: [],
      members: [
        {id: 4, name: 'Miriam', cant: 1, force: false}, {id: 5, name: 'Diógenes y Cintia', cant: 2, force: false}, {id: 6, name: 'Víctor y Clara', cant: 2, force: false},
        {id: 7, name: 'Pablo', cant: 1, force: false}, {id: 8, name: 'Jessy y Ruth', cant: 2, force: false}, {id: 9, name: 'Victor', cant: 1, force: false},
        {id: 10, name: 'Sara', cant: 1, force: false}, {id: 11, name: 'Nacho y Paloma', cant: 2, force: false}, {id: 12, name: 'Juan', cant: 1, force: false},
        {id: 13, name: 'Manuel', cant: 1, force: false}, {id: 14, name: 'Eloisa', cant: 1, force: false}, {id: 15, name: 'Gregorio', cant: 1, force: false},
        {id: 16, name: 'Cristina', cant: 1, force: false}, {id: 17, name: 'Ana', cant: 1, force: false}, {id: 18, name: 'Luis y Maria', cant: 2, force: false},
        {id: 19, name: 'Isabel', cant: 1, force: false}
      ],
      groups: [],
      totalGroups: null,
      idTable: 'table-groups',
      year: new Date().getFullYear(),
    }
  },
  methods: {
    forceRerender() {
      this.componentKey += 1;
    },
    createGroups() {
      this.forceRerender();
      const persons = _.shuffle(this.members)
      const personsForced = _.shuffle(
          [{id: 1, name: 'Mar', cant: 1, force: true}, {id: 2, name: 'Carlos', cant: 1, force: true}, {id: 3, name: 'Rut', cant: 1, force: true}, {}]
      )
      const groups = [];
      // var tempGroups = [];
      while (persons.length) {
        groups.push(persons.splice(0, 4));
      }

      /*var c = 0;
      persons.forEach((person) => {
        console.log(tempGroups)
        if(c >= 4){
          groups.push(tempGroups)
          tempGroups = [];
          c = 0;
        }
        tempGroups.push(person);
        c += person.cant;
      });*/
      groups.push(personsForced);
      this.groups = groups;

      this.totalGroups = Math.ceil(this.members.length / 6);
    },
    exportPDF() {
      const pdf = new jsPDF();
      let tipoGrupo =  document.getElementById('tipoGrupo').selectedOptions[0].text
      let esPreparacion = this.tipoGrupo === '2';
      let widthTable = esPreparacion ? 50 : 'auto';
      let margin = esPreparacion ? {top: 40, left: 80} : {top: 40};
      let messageTitle = esPreparacion ? 'Grupo de ' : 'Grupos de '

      // HEADER
      autoTable(pdf, {
        theme: 'plain',
        tableWidth: 'auto',
        columnStyles: {subTitle: {halign: 'center'}},
        body: [
            {subTitle:
                  {content: messageTitle + tipoGrupo,
                    styles: {fontSize: 20}
                  }
            },
          {subTitle:
                {content:this.subTitle,
                  styles: {fontSize: 17, fontStyle: 'bolditalic'}
                }
          },
        ],
        columns: [
          { header: '', dataKey: 'subTitle' },
        ],
      })

      // BODY
      autoTable(pdf, {html: '#table-groups', tableWidth: widthTable, margin: margin})

      // var pageSize = pdf.internal.pageSize
      // var pageWidth = pageSize.width ? pageSize.width : pageSize.getWidth()
      var arrayComments = [];
      if (this.items.length) {
        this.items.forEach((item) => {
          arrayComments.push({notes: item})
        })

        autoTable(pdf, {
          columnStyles: {notes: {halign: 'left'}},
          body: arrayComments,
          theme: 'plain',
          tableWidth: 'auto',
          columns: [
            {header: 'Notas Importantes ', dataKey: 'notes'},
          ],
          margin: {top: 30}
        })
      }
      pdf.save('grupos.pdf')
    },
    add: function () {
      if (this.comments.length) {
        this.items.push(this.comments)
        this.comments = '';
      }
      this.focusInput();
    },
    remove: function (index) {
      if (this.items.length)
        this.items.splice(index, 1)
    },
    focusInput() {
      this.$nextTick(() => {
        // This callback will only be called after the
        // DOM has been updated
        this.$refs.comments.focus();
      });
    },
    recargarGrupos: function(){
      if(this.groups.length)
        this.createGroups();
    }
  },
  computed: {
    buttonMessage: function () {
      let message = '';
      switch (this.tipoGrupo) {
        case "1":
          message = this.groups.length ? "Volver a generar" : "Generar grupos"
              break
        case "2":
          message = this.groups.length ? "Volver a generar" : "Generar grupo"
              break
      }
      return message;
    },
  }
}
</script>

<style>
.material-icons {
  padding-left: 5px;
  vertical-align: middle;
}

.custom-input {
  border: none;
  font-size: large;
}

.btn-custom {
  margin-right: 10px;
}

.comments {
  padding-bottom: 10px;
}

.list-item {
  font-size: medium;
  transform: translateX(30px);
}

.list-enter-active, .list-leave-active {
  opacity: 0;
  transform: translateX(120px);
  transition: all 1s;
}

.list-enter, .list-leave-to {
  opacity: 0;
  transform: translateX(120px);
  animation: ease-in;
}

.list-enter-to {
  opacity: 1;
  transform: translateX(30px);
  animation: ease-out;
}
</style>