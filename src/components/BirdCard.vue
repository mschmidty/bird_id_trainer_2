<template>
  <div class="card" :class="answerCheckClass">
      <img :src="img_path" alt="Image of Bird">
      <h2>{{ name }}</h2>
      <p>{{ species }}</p>
      <audio controls :src="audio_path + '#t=00:00:10'" ></audio>
      <select 
        v-if="show_study_select !== false" 
        v-model = "study_option_selection" 
        name="birdOptions" 
        id="birdOptions" 
        class="bird-options"
      >
        <option value="">--Please choose an option--</option>
        <option 
          v-for="option in mixedStudyList"
          :key="option.id" 
          :value="option.common_name"
        >{{ option.common_name }}</option>
      </select>
  </div>
</template>

<script>
  export default {
    name: 'birdCard',
    props: {
      name: String,
      species: String,
      audio_path: String,
      img_path: String,
      study_select: Array,
      show_study_select: {
        default: false
      }
    },
    data(){
      return {
        study_option_selection: "",
        speciesToCheck: this.name,
        answerCheckClass:"incorrect-answer",
        studyList: this.study_select
      }
    },
    computed: {
      mixedStudyList: function(){
        return [...this.study_select].sort((a,b)=>{
          return a.common_name.localeCompare(b.common_name)
        })
      }
    },
    methods: {
  
    },
    watch: {
      study_option_selection: function(val){
        if(val==this.speciesToCheck){
          this.answerCheckClass="correct-answer"
        }else{
          this.answerCheckClass="incorrect-answer"
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
.card{
  position:relative;
  padding-bottom: 30px;
  border-radius: 20px;
  background: #eeeff0;
  box-shadow:  2px 2px 5px rgba(0,0,0,0.2),
             -2px -2px 5px #ffffff;
  overflow: hidden;
}
.card img{
  width:100%;
  position:relative;
  display:block;
  margin-bottom: 10px;

}
.card h2, .card p {
  width: 95%;
  margin: 0 auto;
  padding:5px 0;
}
.card p {
  font-style: italic;
  color: #AAAAAA;
  margin-bottom: 15px;
}
.card audio{
  display:block;
  border-radius: 30px;
  margin-top:15px;
  width: 95%;
  margin: 0 auto;
  color: #eeeff0;
  background: #eeeff0;
  box-shadow:  2px 2px 5px rgba(0,0,0,0.2),
             -2px -2px 5px #ffffff;
}
.both, .images, .songs{
  h2, p {
    display:none;
  }
}
.images {
  audio{
    display:none;
  }
}
.songs{
  padding-top: 3rem;
  img {
    display:none;
  }
}
</style>