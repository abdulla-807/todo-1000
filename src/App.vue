<template>
  <div>
    <Header @getSearch="search = $event" :lang="lang" @changeLang="changeLang"/>
    <Notes :notes="filterNotes" @delNote="delNote" @changeNote="changeNote" :lang="lang"/>
    <Modal v-show="modalOpen" 
    @closeModal="closeModal" 
    :currentId="currentId" 
    @addNote="addNote" 
    :edit="edit"
    :editNote="editNote"
    @editedNote="editedNote"
    :lang="lang"
    />
    <AddButton @openModal="openModal" />
  </div>
</template>

<script>
import Header from './components/Header.vue';
import Notes from './components/Notes.vue';
import Modal from './components/Modal.vue';
import AddButton from './components/AddButton.vue';
import langs from './lang';
// import { vasya, petya } from "./lang";
// console.log(langs);
// console.log(vasya);
// console.log(petya);
export default {
  components: {
    Header,
    Notes,
    Modal,
    AddButton
  },
  data() {
    return {
      notes: [
        {
          id: 1,
          title: 'Vue',
          desc: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor',
          date: '07.03.2022'
        },
        {
          id: 2,
          title: 'React',
          desc: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor',
          date: '15.08.2023'
        },
        {
          id: 3,
          title: 'HTML',
          desc: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor',
          date: '20.05.2021'
        },
      ],
      modalOpen: false,
      currentId: 4,
      edit: false,
      editNote: {},
      search: '',
      lang: 'ru',
      langwords: {}
    }
  },
  computed: {
    filterNotes(){
      const items = this.notes.filter((elem)=>{
        let result = elem.title.toLowerCase().includes(this.search.toLowerCase());
        return result;
      })
      return items;
    }
  },
  methods: {
    openModal() {
      this.modalOpen = true;
    },
    closeModal() {
      this.modalOpen = false;
      setTimeout(() => {
        this.edit = false;        
      }, 500);
    },
    addNote(item) {
      this.notes.push(item)
    },
    delNote(id) {
      let index = this.notes.findIndex((elem) => elem.id == id);
      this.notes.splice(index, 1)
    },
    getNotes(){
      const localNotes = localStorage.getItem('notes')
      let result = JSON.parse(localNotes)
      if (result) {
        this.notes = result;
        let last = this.notes.length - 1;
        this.currentId = last >= 0 ? this.notes[last].id + 1 : 1
      }
    },
    changeNote(id){
      let value = this.notes.find( (elem)=>{ return elem.id == id } );
      this.editNote = value;
      this.edit = true;
      this.modalOpen = true;
    },
    editedNote(item){
      this.notes.forEach((elem)=>{
        if (elem.id == item.id) {
          elem.title = item.title;
          elem.desc = item.desc;
          elem.date = item.date;
        }
      })
    },
    changeLang(val){
      this.lang = val;
      localStorage.setItem('lang', val)
    }
  },
  watch: {
    notes: {
      handler(val, oldVal) {
        localStorage.setItem('notes', JSON.stringify(this.notes))
      },
      deep: true
    }
  },
  created(){
    this.getNotes();
    this.langwords = langs;
    this.lang = localStorage.getItem('lang') || 'ru'
  },
  provide: {
    words: langs
  }
}
</script>

<style lang="scss"></style>