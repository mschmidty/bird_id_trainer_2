<template>
  <div>
    <section class="study-options wrap">
      <h3>What Are you interested in Studying?</h3>
      <div class="study-type-options">
        <select v-model="study_type">
          <option disabled value="">Please select one</option>
          <option value="review">Review</option>
          <option value="both">Images and Songs</option>
          <option value="images">Images</option>
          <option value="songs">Songs</option>
        </select>
      </div>
      <h3>Study Options:</h3>
      <div class="study-type-options">
        <select v-model="study_options">
          <option disabled value="">Please select one</option>
          <option value="all">All</option>
          <option value="random">Random</option>
          <option value="select">Select from List</option>
        </select>
        <input v-if="study_options=='random'" type="text" v-model="random_number">
      </div>
      <div class="study-list">
        <div class="select-options" v-if="study_options=='select'">
          <div class="checkbox-option" v-for="bird in birds" :key="bird.id">
            <input 
              type="checkbox" 
              id="bird.id" 
              name="bird.common_name" 
              :value="bird.common_name" 
              v-model="selectedNames"
            >
            <label for="bird.common_name">{{bird.common_name}}</label>
          </div> 
        </div>
      </div>
      <button @click="mixUpCards" class="white-button">Mix Up</button>
    </section>
    <section class="card-wrap" :class="{'check-answers': check_answers}">
      <bird-card
        v-for="bird in birdsFiltered"
        :class="study_type"
        :key="bird.id"
        :name="bird.common_name"
        :species="bird.species"
        :audio_path="bird.audio_file_path"
        :img_path="'images/'+ bird.common_name + '.jpg'"
        :study_select="studySelectOptions"
        :show_study_select="show_study_select"
      ></bird-card>
    </section>
    <section class="test-options wrap">
      <button @click.prevent="check_answers = true" class="white-button">Check Answers</button>
      <a @click="check_answers = false" class="white-button">Keep Studying</a>
    </section>
  </div>
  
</template>

<script>
  import BirdCard from "@/components/BirdCard.vue";

  export default {
    name: "Home",
    components: {BirdCard},
    data(){
      return {
        birds:[],
        birdsFiltered:[],
        study_type: "review",
        study_options: "all",
        random_number: 9,
        show_study_select: false, 
        studySelectOptions: [],
        check_answers: false,
        selectedNames: []
      }
    },
    mounted(){
      fetch("../data/cleaned_list_with_filenames.csv")
        .then((response) => response.text())
        .then((data) => {
          const lines = data.trim().split("\n");
          let result = [];
          const headers = lines[0].trim().split(",")

          for(let i = 1; i<lines.length; i++){
            let obj = {}
            let currentLine = lines[i].split(",")
            for(let j = 0; j<headers.length; j++){
              obj[headers[j]] = currentLine[j]
            }
            result.push(obj)
          }
          const finalBirds = this.birdsFiltered = this.birds = result.map((bird, index)=>{
            return {
              id: index,
              ...bird
            }
          })

          this.birdsMixed = this.orderBirds(finalBirds)
          
        })
    },
    methods: {
      filterRandom(num){
        const newBirds = this.birds;
        this.birdsFiltered = this.shuffleBirds(newBirds).slice(0,num)
      },
      shuffleBirds(array){
        let arrayLength = array.length, temp, index;
        while(arrayLength >0){
          index = Math.floor(Math.random()*arrayLength)
          arrayLength--
          temp = array[arrayLength]
          array[arrayLength] = array[index]
          array[index] = temp
        }
        return array;
      },
      orderBirds(birds){
        return birds.sort((a,b)=>{
          return a.common_name.localeCompare(b.common_name)
        })
      },
      mixUpCards(){
        const newBirds = this.shuffleBirds(this.birdsFiltered)
        this.birdsFiltered = newBirds
      },
    },
    watch: {
      study_type: function(val){
        if(val!=="review"){
          this.show_study_select=true
        }else{
          this.show_study_select=false
        }
        
        if(val==="both"){

        }
      },
      study_options: function(val){
        if(val==="random"){
          this.filterRandom(this.random_number)
        }
        else{
          const newFilteredBirds = [...this.birds].sort((a,b)=>{
            return a.common_name.localeCompare(b.common_name)
          })
          this.birdsFiltered = newFilteredBirds;
        }
        if(val!="select"){
          this.selectedNames = []
        }
      },
      random_number: function(val){
        this.filterRandom(val)
      },
      selectedNames: function(val){
        if(this.study_options=="select"){
          const currentBirds = this.birds
          this.birdsFiltered = currentBirds.filter(element=>{
            return val.includes(element.common_name)
          })
        }
      },
      birdsFiltered: function(val){
        const numNewBirds = (val.length)/2
        const newBirds = [...this.birds];
        const randomBirds = this.shuffleBirds(newBirds).slice(0,6)
        this.studySelectOptions = [...new Set([...randomBirds, ...val])]
      }
    }
  }
</script>

<style lang="scss" scoped>
.card-wrap{
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  column-gap: 3rem;
  row-gap: 3rem;

  width: 95%;
  max-width: 1600px;
  margin: 3rem auto;

  @media all and (max-width: 1200px) and (min-width: 600px){
    grid-template-columns: 1fr 1fr;
  }

  @media all and (max-width: 750px) and (min-width:600px){
    column-gap: 1.5rem;
    row-gap: 1.5rem;
  }
  @media all and (max-width: 599px){
    grid-template-columns: 1fr;
  }
}
.study-type-options {
  margin-bottom: 3rem;
}
.white-button{
  background: #eeeff0;
  color: #076AC9;
  font-size: 1.1rem;
  box-shadow:  2px 2px 5px rgba(0,0,0,0.2),
             -2px -2px 5px #ffffff;
  padding:10px 20px;
  border-radius:5px;
  text-align: center;
  font-weight: bold;
  border: 2px solid #076AC9;
  margin-right: 1.5rem;
}
.select-options {
  display:flex;
  flex-wrap: wrap;
  margin-bottom: 2rem;
  div {
    width: 30%;
  }
}
.check-answers{
  .correct-answer{
    box-shadow:0 0 20px #01CA23;
  }

  .incorrect-answer{
    box-shadow:0 0 20px #FF5733;
  }
  
}
</style>
