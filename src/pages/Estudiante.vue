<template>
  <q-page>
    <div class="row">
      <div class="col-12">
        <q-form @submit.prevent="crear">
          <div class="row q-pa-xs">
            <div class="col-3">
              <q-input outlined dense label="cI" required v-model="estudiante.ci"/>
            </div>
            <div class="col-3">
              <q-input outlined dense label="nombres" required v-model="estudiante.nombres"/>
            </div>
            <div class="col-3">
              <q-input outlined dense label="Paterno" required v-model="estudiante.paterno"/>
            </div>
            <div class="col-3">
              <q-input outlined dense label="Materno" required v-model="estudiante.materno"/>
            </div>
            <div class="col-3">
                <q-select dense outlined label="Sexo" v-model="estudiante.sexo" :options="['Masculino','Femenino']"/>
            </div>
            <div class="col-3">
              <q-input type="date" outlined dense label="fechanac" required v-model="estudiante.fechanacimiento"/>
            </div>
            <div class="col-3">
              <q-input outlined dense label="Celular" required v-model="estudiante.celular"/>
            </div>
            <div class="col-3 flex flex-center">
              <q-btn type="submit" outlined dense icon="send" :color="boolcrear?'positive':'yellow'" :label="boolcrear?'Insertar':'Modificar'"/>
            </div>
          </div>
        </q-form>
      </div>
      <div class="col-12">
        <q-table title="Lista de Estudiantes" :columns="columns" :rows="estudiantes" dense>
          <template v-slot:body-cell-opcion="props">
            <q-tr :props="props">
              <q-td key="opcion" :props="props">
                <q-btn @click="eliminar(props.row)" size="xs" icon="delete" color="negative"  label="Eliminar"/>
                <q-btn @click="modificar(props.row)" size="xs" icon="edit" color="primary"  label="Modifica"/>
              </q-td>
            </q-tr>
          </template>
        </q-table>
      </div>
    </div>
  </q-page>
</template>
<script>
import {date} from 'quasar'
export default {
  data(){
    return {
      estudiantes:[],
      estudiante:{},
      boolcrear:true,
      columns:[
        {name:'id',label:'#',field:'id'},
        {name:'ci',label:'CI',field:'ci'},
        {name:'nombres',label:'Nombres',field:'nombres'},
        {name:'paterno',label:'Paterno',field:'paterno'},
        {name:'materno',label:'Materno',field:'materno'},
        {name:'fechanacimiento',label:'fechanacimiento',field:'fechanacimiento'},
        {name:'sexo',label:'sexo',field:'sexo'},
        {name:'celular',label:'celular',field:'celular'},
        {name:'opcion',label:'Opcion',field:'opcion'},
      ]
    }
  },
  created() {
    this.misdatos();
    this.reset();
  },
  methods:{
    crear(){
      this.$q.loading.show()
      if (this.boolcrear)
        this.$api.post('/estudiante',this.estudiante).then(res=>{
          this.reset();
          this.misdatos()
          this.$q.loading.hide()
        })
      else
        this.$api.put('/estudiante/'+this.estudiante.id,this.estudiante).then(res=>{
           this.reset();

          this.misdatos()
          this.boolcrear=true
        })
    },
    misdatos(){
      this.$api.get('/estudiante').then(res=>{
        this.estudiantes=res.data
        console.log(this.estudiantes)
      })
    },
    eliminar(estudiante){
      if (confirm("Seguro de eliminar?"))
        this.$api.delete('/estudiante/'+estudiante.id).then(res=>{
          this.misdatos()
          this.reset
        })
    },
    modificar(estudiante){
      this.estudiante=estudiante
      this.boolcrear=false
    },
    reset(){
      this.estudiante.fechanacimiento=date.formatDate(Date.now(),'YYYY-MM-DD');
      this.estudiante.ci='';
      this.estudiante.nombres='';
      this.estudiante.paterno='';
      this.estudiante.materno='';
      this.estudiante.celular='';
      this.estudiante.sexo='';
    }
  }
}
</script>

<style scoped>

</style>
