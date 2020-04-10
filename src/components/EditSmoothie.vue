<template>
  <div v-if="smoothie" class="edit-smoothie container">
    <h2>Edit {{smoothie.title}} Smoothie</h2>
    <form @submit.prevent="EditSmoothie">
      <div class='field title'>
        <label for='title'>Smoothie Title:</label>
        <input type='text' name="title" v-model="smoothie.title">
      </div>
      <div v-for="(ing, index) in smoothie.ingredients" :key="index" class="field">
        <label for='ingredient'>Ingredients</label>
        <input type='text' name="ingredient" v-model="smoothie.ingredients[index]">
        <i class='material-icons delete' @click="deleteIng(ing)">delete</i>
      </div>
      <div class='field add-ingredient'>
        <label for='add-ingredient'>Add an ingredient:</label>
        <input type='text' name="add-ingredient" v-model="another" @keydown.tab.prevent="addIng">
      </div>
      <div class='field center-align'>
        <p v-if="feedback" class="red-text">{{feedback}}</p>
        <button class="btn pink">Edit Smoothie</button>
      </div>
    </form>
  </div>
</template>

<script>
import db from '../firebase/init'
import slugify from 'slugify'

export default {
  name: 'EditSmoothie',
  data() {
    return {
      smoothie: null,
      another: null,
      feedback: null
    }
  },

  methods: {
    EditSmoothie() {
      //create url slug
      this.smoothie.slug = slugify(this.smoothie.title, {
        //any time there is a space, replace with -
        replacement: '-',
        remove: /[$*_+~.()'"!\-:@]/g,
        lower: true
      })

      if (this.smoothie.title) {
        //.doc will grab the one smoothie that matches to the id
        db.collection('smoothies').doc(this.smoothie.id).update({
          title: this.smoothie.title,
          ingredients: this.smoothie.ingredients,
          slug: this.smoothie.slug
        }).then(() => {
          this.$router.push({name: 'Index'})
        }).catch(err => {
          console.log(err);
        })
        this.feedback = null
      }
      else{
        this.feedback = 'You must enter a smoothie title'
      }
    },

    addIng() {
      if (this.another) {
        this.smoothie.ingredients.push(this.another)
        this.another = null
        this.feedback = null

      }
      else{
        this.feedback = 'You must enter a value to add ingredient'
      }
    },

    deleteIng(ing){
      this.smoothie.ingredients = this.smoothie.ingredients.filter(ingredient => {
        return ingredient != ing
      })
    }
  },

  created() {
    //get me the documents in db where slug is equal to slug from route param
    let ref = db.collection('smoothies').where('slug', '==', this.$route.params.smoothie_slug)
    //to actually get the data in ref do this
    ref.get().then(snapshot => {
      //cycle through what our references return (only one with this slug)
      snapshot.forEach( doc => {
        this.smoothie = doc.data;
        this.smoothie.id = doc.id;
        //then grab the data for what we cycled through
        //and store in data
        console.log(doc.data());
      })
    })
  }
}
</script>

<style>
 .edit-smoothie {
    margin-top: 60px;
    padding: 20px;
    max-width: 500px;
  }

  .edit-smoothie h2 {
    font-size: 2em;
    margin: 20px auto;
  }

  .edit-smoothie .field{
    margin: 20px auto;
    position: relative;
  }

  .edit-smoothie .delete {
    position: absolute;
    right: 0;
    bottom: 16px;
    color: #aaa;
    font-size: 1.4em;
    cursor: pointer;
  }
</style>