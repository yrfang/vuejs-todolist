<template lang="pug">
.CreateNote
  header
    a.backToList
      span(@click="goBack")
        i.fa.fa-chevron-left
      span.menuText {{ undefined !== this.id ? 'Editing Note' : 'Create Note' }}
    a(@click="saveNote")
      button.saveNote Save
  .content
    input.title(placeholder="Note title is required", v-model="note.title")
    input.tag(placeholder="Note tag", v-model="note.tag")
    textarea(placeholder="Note...", v-model="note.text")
</template>

<script>
const uuidV4 = require('uuid/v4');

export default {
  props: ['id'],
  data() {
    return {
      note: {},
      // noteIndex: -1,
    }
  },
  mounted() {
    if (undefined !== this.id) {
      this.loadNote();
    }
  },
  methods: {
    loadNote() {
      const getLocalNotes = localStorage.getItem('vuejs-note');
      const notes = JSON.parse(getLocalNotes);


      if (undefined !== this.id) {
        this.note = notes.find((note) => note.id === this.id);
      } else {
        this.$router.push('/');
      }
    },
    saveNote() {
      const getLocalNotes = localStorage.getItem('vuejs-note');
      const notes = (null === getLocalNotes?[]:JSON.parse(getLocalNotes));

      const title = document.querySelector('.title');
      const createTextarea = document.querySelector('textarea');

      const editedNote = {
        title: this.note.title,
        id: this.note.id || uuidV4(),
        meta: Date.now(),
        tag: this.note.tag,
        text: this.note.text,
      };

      const meetIndex = notes.findIndex((note) => note.id === this.id);

      if (title.value.trim() == '') {
        return this.$router.push('/');
      } else if (undefined !== meetIndex && meetIndex >= 0) {
        notes[meetIndex] = editedNote;
      } else {
        notes.push(editedNote);
      }

      localStorage.setItem('vuejs-note', JSON.stringify(notes));
      this.$router.push('/');
    },
    goBack() {
      this.$router.push('/');
    },
  }
}
</script>

<style lang="sass" scoped>
header
  padding: 10px 20px
  background-color: #eee
  color: #555
  font-size: 20px
  font-weight: 100
  position: relative

  i.fa.fa-chevron-left
    margin-right: 20px
    padding: 10px
    padding-right: 20px
    border-right: solid 2px #dbdbdb
    cursor: pointer

  .menuText
    color: #36D1DC
    font-size: 24px
    font-weight: bold

  button.saveNote
    border: solid 1px rgba(#000, 0.2)
    border-radius: 5px
    background-color: #366ce2
    color: #fff
    font-size: 18px
    padding: 5px 8px
    cursor: pointer
    position: absolute
    top: 10px
    right: 30px
    &:focus
      outline: none
    &:hover
      background-color: rgba(#366ce2,0.7)

.content
  margin-top: 40px
  margin-left: auto
  margin-right: auto
  padding: 10px 20px
  width: 60%
  border: solid 1px rgba(#366ce2,0.5)
  border-radius: 5px

  input, textarea
    width: 100%
    margin-top: 10px
    margin-bottom: 10px
    padding: 5px 10px
    border-radius: 5px
    border: solid 1px rgba(#000,0.2)

  .title
    font-size: 24px
    font-weight: bold
    color: #5B86E5
    padding-top: 10px
    padding-bottom: 10px

  textarea
    font-size: 20px
    height: 200px

@media screen and (max-width: 480px)
  header
    padding: 10px
    height: 110px
    width: 100vw

    button.saveNote
      top: 57px
      right: initial
      left: 15px
      width: 90%

  .content
    width: 90%
    margin-left: 15px
    margin-right: 20px
    margin-bottom: 20px
    font-size: 16px

    .title
      font-size: 18px
</style>
