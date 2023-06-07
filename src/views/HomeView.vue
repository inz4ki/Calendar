<script>
import FullCalendar from '@fullcalendar/vue3'
import dayGridPlugin from '@fullcalendar/daygrid'
import interactionPlugin, { Draggable } from '@fullcalendar/interaction'
import timeGridPlugin from '@fullcalendar/timegrid'
import listPlugin from '@fullcalendar/list';
import axios from 'axios';
import Modal from '../components/Modal.vue';
window.bootstrap = require('bootstrap/dist/js/bootstrap.bundle.js');

export default {
  components: {
    FullCalendar,
    Modal // make the <FullCalendar> tag available
  },
  mounted() {
    axios.get('http://localhost:81/laravel/teste2/public/api/event/show').then(response => {
      this.calendarOptions.events = response.data;
    })
  },
  methods: {
    salvarData() {
      if (this.tempEvents.end > this.tempEvents.start) {
        axios.post('http://localhost:81/laravel/teste2/public/api/event/store', this.tempEvents).then(response => {
          axios.get('http://localhost:81/laravel/teste2/public/api/event/show').then(response => {
            this.calendarOptions.events = response.data;
            this.tempEvents.title = '';
            this.tempEvents.start = '';
            this.tempEvents.end = '';
            this.tempEvents.description = '';
          })
        }).catch((error) => {
          if (error.response.status = 500) {
            alert('Adicione Mais Informações para Salvar o Evento!');
          }
        })
      } else {
        alert('Adicione uma data Valida!!')
      }


    },
    deletarEvento() {
      let id = document.getElementById('id').textContent;
      console.log(id)
      if (confirm('Você deseja realmente Excluir o Evento?') == true) {
        axios.delete('http://localhost:81/laravel/teste2/public/api/event/' + id,).then(response => {
          axios.get('http://localhost:81/laravel/teste2/public/api/event/show').then(response => {
            this.calendarOptions.events = response.data;
          }).catch()
        })
      }
      else { }
    },
    editar() {
      this.editavel = !this.editavel
      console.log(this.editavel)
    },
    resetarEditavel() {
      this.editavel = false
    },
    SalvarEdicao() {
      this.tempEvents.title = document.getElementById('titleEdt').value;
      this.tempEvents.start = document.getElementById('startEdt').value;
      this.tempEvents.end = document.getElementById('endEdt').value;
      this.tempEvents.description = document.getElementById('descriptionEdt').value;
      this.id = document.getElementById('idEdt').value;

      if (this.tempEvents.end > this.tempEvents.start) {
        axios.post('http://localhost:81/laravel/teste2/public/api/event/edit/' + this.id, this.tempEvents).then(response => {
          axios.get('http://localhost:81/laravel/teste2/public/api/event/show').then(response => {
            this.calendarOptions.events = response.data;
            var moment = require('moment');
            this.tempEvents.start = moment(this.tempEvents.start).format('DD/MM/YYYY HH:mm');
            this.tempEvents.end = moment(this.tempEvents.end).format('DD/MM/YYYY HH:mm');
            document.getElementById('title').textContent=this.tempEvents.title
            document.getElementById('start').textContent=this.tempEvents.start
            document.getElementById('end').textContent=this.tempEvents.end
            document.getElementById('description').textContent=this.tempEvents.description
            this.tempEvents.title = '';
            this.tempEvents.start = '';
            this.tempEvents.end = '';
            this.tempEvents.description = '';
            alert('Alterações Salvas!!');
            this.resetarEditavel();
            axios.get('http://localhost:81/laravel/teste2/public/api/event/show').then(response => {
              this.calendarOptions.events = response.data;
            })
          })
        }).catch((error) => {
          if (error.response.status = 500) {
            alert('Adicione Mais Informações para Salvar o Evento!');
          }
        })
        console.log('salvou')
      } else {
        alert('Adicione uma data Valida!!')
      }



    }
  },
  data() {
    return {
      calendarOptions: {
        plugins: [dayGridPlugin, interactionPlugin, timeGridPlugin, listPlugin],
        initialView: 'timeGridWeek',
        locale: 'br',
        buttonText: {
          today: 'Hoje',
          month: 'Mês',
          week: 'Semana',
        },
        headerToolbar: {
          left: 'prev,today,next',
          center: 'title',
          right: 'dayGridMonth,timeGridWeek',
          today: 'Hoje',
          month: 'Mês',
          week: 'Semana',
        },
        titleFormat: { // will produce something like "Tuesday, September 18, 2018"
          month: 'long',
          year: 'numeric',
        },
        selectable: true,
        droppable: true,
        editable: true,
        events: [],
        selectHelper: true,
        eventColor: '#6c7fed',
        eventDrop: function (e) {
          console.log(e)
          let id = e.event.id
          let title = e.event.title
          let start = e.event.startStr
          let end = e.event.endStr
          let description = e.event._def.extendedProps.description
          let form = new FormData
          form.append('title', title)
          form.append('start', start)
          form.append('end', end)
          form.append('description', description)
          axios.post(`http://localhost:81/laravel/teste2/public/api/event/edit/${id}`, form, {
            headers: {
              'Content-Type': 'multipart/form-data'
            },
          },
          )
        },
        eventResize: function (e) {
          console.log(e)
          let id = e.event.id
          let title = e.event.title
          let start = e.event.startStr
          let end = e.event.endStr
          let description = e.event._def.extendedProps.description
          let form = new FormData
          form.append('title', title)
          form.append('start', start)
          form.append('end', end)
          form.append('description', description)
          axios.post(`http://localhost:81/laravel/teste2/public/api/event/edit/${id}`, form, {
            headers: {
              'Content-Type': 'multipart/form-data'
            },
          },
          )
        },
        eventClick: function (e) {
          var moment = require('moment');
          console.log(e.event);
          var id = e.event.id
          var title = e.event.title
          var start = e.event.startStr
          var end = e.event.endStr
          var description = e.event._def.extendedProps.description

          start = moment(start).format('YYYY-MM-DDTHH:mm');
          end = moment(end).format('YYYY-MM-DDTHH:mm');
          document.getElementById('idEdt').value = id;
          document.getElementById('titleEdt').value = title;
          document.getElementById('startEdt').value = start;
          document.getElementById('endEdt').value = end;
          document.getElementById('descriptionEdt').value = description;

          start = moment(start).format('DD/MM/YYYY HH:mm');
          end = moment(end).format('DD/MM/YYYY HH:mm');

          document.getElementById('id').textContent = id;
          document.getElementById('title').textContent = title;
          document.getElementById('start').textContent = start;
          document.getElementById('end').textContent = end;
          document.getElementById('description').textContent = description;
          const myModal = new bootstrap.Modal('#myModal')
          myModal.show();
        }
      },
      tempEvents: {
        title: '',
        start: '',
        end: '',
        description: ''
      },
      id: '',
      editavel: false
    }
  },

}
</script>
<template>
  <div class="row justify-content-md-center">
    <div class="col-md-9">
      <FullCalendar :options="calendarOptions" />
    </div>
    <div class="col-md-2 border ">
      <h3>Cadastrar Data</h3>
      <hr>
      <label>Titulo</label>
      <input class="input-group input-group-sm mb-3" v-model="tempEvents.title" type="text">
      <hr>
      <label>Data Inicial</label>
      <input class="input-group input-group-sm mb-3" v-model="tempEvents.start" type="datetime-local">
      <hr>
      <label>Data De Termino</label>
      <input class="input-group input-group-sm mb-3" v-model="tempEvents.end" type="datetime-local">
      <hr>
      <label>Descrição</label>
      <textarea rows="12" cols="60" class="input-group input-group-sm mb-3" v-model="tempEvents.description"
        type="text"></textarea>
      <button class="btn btn-success" @click="salvarData()">Salvar</button>
    </div>
  </div>

  <!-- MODAL -->
  <div class="modal modal-xl fade " id="myModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">Informações Do Evento</h5>
          <button type="button" class="btn-close" @click="resetarEditavel()" data-bs-dismiss="modal"
            aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <div v-show="this.editavel === false">
            <div class="row">
              <div class="col-md-4">
                <h2 class="bold">Data de Inicio</h2>
                <p class="fw-bolder" id="start"></p>
                <h2 class="bold">Data de Termino</h2>
                <p class="fw-bolder" id="end"></p>
                <div v-show="false">
                  <h2>Id</h2>
                  <p id="id"></p>
                </div>
              </div>
              <div class="col-md-4">
                <h2>Titulo</h2>
                <p class="fw-bolder" id="title"></p>
              </div>
              <div class="col-md-4">
                <h2>Descrição</h2>
                <p id="description" class="border text-break fw-bolder "></p>
              </div>
            </div>
          </div>
          <div v-show="this.editavel === true">
            <div class="row">
              <div class="col-md-4">
                <h2 class="bold">Data de Inicio</h2>
                <input value="" type="datetime-local" class="fw-bolder" id="startEdt">
                <h2 class="bold">Data de Termino</h2>
                <input value="" type="datetime-local" class="fw-bolder" id="endEdt">
                <div v-show="false">
                  <h2>Id</h2>
                  <p id="idEdt"></p>
                </div>
              </div>
              <div class="col-md-4">
                <h2>Titulo</h2>
                <textarea value="" rows="1" cols="20" class="fw-bolder text-center" id="titleEdt"></textarea>
              </div>
              <div class="col-md-4">
                <h2>Descrição</h2>
                <textarea value="" rows="6" cols="20" id="descriptionEdt"
                  class="border text-center text-break fw-bolder"></textarea>
              </div>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" v-if="editavel === false" @click="editar()" class="btn btn-primary">Editar Evento</button>
          <button type="button" v-if="editavel === true" @click="SalvarEdicao()" class="btn btn-success">Salvar
            Edição</button>
          <button type="button" @click="deletarEvento()" class="btn btn-danger" data-bs-dismiss="modal">Deletar
            Evento</button>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
* {
  font-family: Cambria, Cochin, Georgia, Times, 'Times New Roman', serif;
}

input {
  border-radius: 10px;
  text-align: center;
}

.modal-body {
  background-color: rgb(199, 206, 248);
}

.modal-header,
.modal-footer {
  background-color: rgb(255, 255, 255);
}

h2 {
  background-color: rgb(3, 54, 99);
  border-radius: 25px;
  color: white;
}

textarea {
  border-radius: 10px;
  padding: 5px;
  border: solid 1px black;
}

input {
  margin-top: 5px;
  margin-bottom: 20px;
}

p {
  border-radius: 10px;
  background-color: aliceblue;
}
</style>