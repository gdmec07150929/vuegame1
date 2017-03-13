<!--<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Essential Links</h2>
    <ul>
      <li><a href="https://vuejs.org" target="_blank">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank">Forum</a></li>
      <li><a href="https://gitter.im/vuejs/vue" target="_blank">Gitter Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank">Twitter</a></li>
      <br>
      <li><a href="http://vuejs-templates.github.io/webpack/" target="_blank">Docs for This Template</a></li>
    </ul>
    <h2>Ecosystem</h2>
    <ul>
      <li><a href="http://router.vuejs.org/" target="_blank">vue-router</a></li>
      <li><a href="http://vuex.vuejs.org/" target="_blank">vuex</a></li>
      <li><a href="http://vue-loader.vuejs.org/" target="_blank">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank">awesome-vue</a></li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>-->

<!-- Add "scoped" attribute to limit CSS to this component only -->
<!--<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>-->


<template>
  <div id="app">
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">玩家</h1>
        <div class="healthbar">
          <div class="healthbar text-center" style="background-color: green; margin:0 ;color: white;"
          v-bind:style="{width:playerHealth+'%'}">
          {{playerHealth}}
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">妖怪</h1>
        <div class="healthbar">
          <div class="healthbar text-center" style="background-color: green; margin:0 ;color: white;"
          :style="{width:monsterHealth+'%'}">
          {{monsterHealth}}
          </div>
        </div>
      </div>
      
    </section>
 <section class="row controls" v-if="!gameIsRunning">
   <div class="small-12 columns">
     <button id="start-game" @click="startGame">开始游戏</button>
   </div>
 </section>
  <section class="row controls" v-else>
   <div class="small-12 columns">
     <button id="attack" @click="attack">暴击</button>
     <button id="special-attack" @click="specialAttack">放大招</button>
     <button id="heal" @click="heal">治愈</button>
     <button id="give-up" @click="giveUp">投降</button>
   </div>
 </section>
 <section class="row log" v-if="turns.length > 0">
   <div class="small-12 columns">
     <ul>
       <li v-for='turn in turns' :click="{'player-turn': turn.isPlayer, 'monster-turn': !turn.isPlayer}">
         {{turn.text}}
       </li>
     </ul>
   </div>
 </section>
  </div>
</template>

<script>
export default {
  data () {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      gameIsRunning: true,
      turns: [ ]
    }
  },
  methods: {
    startGame: function () {
      this.gameIsRunning = true
      this.playerHealth = 100
      this.monsterHealth = 100
    },
    attack: function () {
      var damage = this.calculateDamage(3, 10)
      this.monsterHealth -= damage
      this.turns.unshift({
        isPlayer: true,
        text: '玩家打击怪物，怪物伤害：' + damage
      })
      if (this.checkWin()) {
        return
      }
      this.monsterAttacks()
    },
    specialAttack: function () {
      let damage = this.calculateDamage(10, 20)
      this.monsterHealth -= damage
      this.turns.unshift({
        isPlayer: true,
        text: '玩家发大招打击怪物，怪物伤害：' + damage
      })
      if (this.checkWin()) {
        return
      }
      this.monsterAttacks()
    },
    heal: function () {
      if (this.playerHealth <= 90) {
        this.playerHealth += 10
      } else {
        this.playerHealth = 100
      }
      this.turns.unshift({
        isPlayer: true,
        text: '玩家治愈10'
      })
      this.monsterAttacks()
    },
    giveUp: function () {
      this.gameIsRunning = false
    },
    calculateDamage: function (min, max) {
      return Math.max(Math.floor(Math.random() * max) + 1, min)
    },
    monsterAttacks: function () {
      let damage = this.calculateDamage(5, 12)
      this.playerHealth -= damage
      this.checkWin()
      this.turns.unshift({
        isPlayer: false,
        text: '怪物打击玩家，玩家伤害：' + damage
      })
    },
    checkWin: function () {
      if (this.monsterHealth <= 0) {
        if (confirm('玩家赢了！再来一局？')) {
          this.startGame()
        } else {
          this.gameIsRunning = false
        }
        return true
      } else if (this.playerHealth <= 0) {
        if (confirm('你输了！不服气，再来！')) {
          this.startGame()
        } else {
          this.gameIsRunning = false
        }
        return true
      }
      return false
    }
  }
}
</script>





<style scoped>
.text-center {
  text-align: center;
}

.healthbar {
  width: 80%;
  height: 40px;
  background-color: #eee;
  margin: auto;
  transition: width 500ms;
}

.controls, .log {
  margin-top: 30px;
  text-align: center;
  padding: 10px;
  border: 1px solid #ccc;
  box-shadow: 0px 3px 6px #ccc;
}

.turn {
  margin-top: 20px;
  margin-bottom: 20px;
  font-weight: bold;
  font-size: 22px;
}

.log ul {
  list-style: none;
  font-weight: bold;
  text-transform: uppercase;
}

.log ul li {
  margin: 5px;
}

.log ul .monster-turn {
  color: red;
  background-color: #ffc0c1;
}

button {
  font-size: 20px;
  background-color: #eee;
  padding: 12px;
  box-shadow: 0 1px 1px black;
  margin: 10px;
}

#start-game {
  background-color: #aaffb0;
}

#start-game:hover {
  background-color: #76ff7e;
}

#attack {
  background-color: #ff7367;
}

#attack:hover {
  background-color: #ff3f43;
}

#special-attack {
  background-color: #ffaf4f;
}

#special-attack:hover {
  background-color: #ff9a2b;
}

#heal{
  background-color: #aaffb0;
}

#heal:hover {
  background-color: #76ff7e;
}

#give-up {
  background-color: #ffffff;
}

#give-up:hover {
  background-color: #c7c7c7;
}
</style>
