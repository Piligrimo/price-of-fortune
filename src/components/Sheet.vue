<template>
  <div class="sheet">
    <div class="grid">
      <div class="grid-item">
        <h1 @click="nameModalVisible=true">{{ state.name }} 🖊️</h1>
        <h2 @click="professionModalVisible=true">{{ professionName || 'Выбери профессию' }} 🖊️</h2>
      </div>
      <div class="grid-item"/>
      <div class="grid-item">
        <table>
          <tr v-for="stat in formattedStats" :key="stat.id">
            <td align="left">{{ stat.name }}</td>
            <td>{{ stat.value }}</td>
            <td @click="editStat(stat.id)">🖊️</td>
            <td @click="rollStat(stat.id)"><span v-if="stat.value">🎲</span></td>
          </tr>
        </table>
        <br/>
        <table>
          <tr>
            <td align="left">Здоровье</td>
            <td width="50"><input v-model="state.hp" type="number"></td>
            <td width="20"></td>
            <td @click="state.hp++">➕</td>
            <td @click="state.hp--">➖</td>
          </tr>
          <tr>
            <td align="left">Удача</td>
            <td width="50"><input v-model="state.fortune" type="number"></td>
            <td></td>
            <td @click="resetFortune">⌛</td>       
            <td></td>
          </tr>
          <tr>
            <td align="left">Бонус от удачи</td>
            <td width="50" style="font-size: 24px; height: 64px;">{{ fortuneBonus }}</td>
            <td width="20"></td>
            <td > <span v-if="state.fortune > 0 && fortuneBonus <3" @click="handleFortune(1)">➕</span></td>
            <td > <span v-if="fortuneBonus>0" @click="handleFortune(-1)">➖</span></td>
          </tr>
        </table>
      </div>
      <div class="grid-item">
        <h3>Навыки</h3>
        <table v-if="skills.length">
          <tr v-for="skill in skills" :key="skill.id"> 
            <td align="left" @click="showDescription(skill)">{{ skill.name }} </td>
            <template v-if="skill.maxUses">
              <td >{{ state.skillUses[skill.id] }}</td>
              <td width="20">
                <span 
                  v-if="state.skillUses[skill.id] < skill.maxUses"
                  @click="state.skillUses[skill.id]++"
                >➕</span>
              </td>
              <td width="20">
                <span 
                  v-if="state.skillUses[skill.id] > 0"
                  @click="state.skillUses[skill.id]--"
                >➖</span>
              </td>
            </template>
          </tr>
        </table>
        <p v-else>Навыки пока не получены</p>

        <h3>Причуды</h3>
        <table v-if="state.quirks.length">
          <tr v-for="quirk in state.quirks" :key="quirk.id">
            <td><input type="text" v-model="quirk.name"></td>
            <td width="10"></td>
            <td width="40"><input type="number" v-model="quirk.bonus"></td>
            <td width="10"/>
            <td width="40"><input type="checkbox" v-model="quirk.inUse"></td>
            <td width="40" @click="removeQuirk(quirk.id)">❌</td>
          </tr>
        </table>
        <p v-else>Причуды пока не добавлены</p>
        <button class="add-quirk-button" @click="addQuirk">Добавить</button>
      </div>
    </div>
    <button class="global-save-button" @click="globalSave">Сохранить</button>
    <name-input 
      :visible="nameModalVisible" 
      :name="state.name" 
      @save="saveName"
      @close="nameModalVisible=false"
    />
    <profession-input
      :visible="professionModalVisible" 
      :profession="state.profession" 
      @save="saveProfession"
      @close="professionModalVisible=false"
    />
    <stat-input
      :value="state.stats[selectedStat]"
      :profession="state.profession"
      :visible="statModalVisible"
      :stat="selectedStat"
      :pool="state.statPool"
      @save="saveStat"
      @close="statModalVisible=false"
    />

    <rolling-modal
      :visible="rollModalVisible"
      :quantity="rollValue(selectedStat)"
      @close="finishRoll"
    />   
    <skill-description
      :visible="skillDescriptionVisible"
      :skill="selectedSkill"
      @close="skillDescriptionVisible=false"
    />
  </div>
</template>

<script>
import NameInput from './NameInput.vue';
import ProfessionInput from './ProfessionInput.vue';
import StatInput from './StatInput.vue';
import RollingModal from './RollingModal.vue';
import SkillDescription from './SkillDescription.vue';
import { professionsDictionary, statDictionary, professionSkills, statSkills } from '../utils/dictionaries'

export default {
  name: 'MySheet',
  components: { NameInput, ProfessionInput, StatInput, RollingModal, SkillDescription },
  data() {
    return {
      state: {
        name: 'Выбери имя',
        profession: '',
        stats: {
          FIG: 0,
          TRI: 0,
          CHA: 0,
          EXP: 0,
          SHI: 0,
          MIS: 0,
        },
        statPool: [1,1,2,2,3,3],
        hp: 13,
        fortune: 13,
        skillUses: {},
        quirks: []
      },
      fortuneBonus: 0,
      selectedStat: null,
      selectedSkill: null,
      nameModalVisible: false,
      professionModalVisible: false,
      statModalVisible: false,
      rollModalVisible: false,
      skillDescriptionVisible: false,
    }
  },
  watch: {
    skills(val) {
      val.forEach(({id, maxUses}) => {
        if (maxUses && !this.state.skillUses[id]) {
          this.$set(this.state.skillUses, id, maxUses)
        }
      })
    }
  },
  mounted() { 
    const state =  JSON.parse(localStorage.getItem('state'))
    if (state)
      this.state ={ ...this.state, ...state}
  },
  methods: {
    saveName(e) {
      this.state.name = e
      this.nameModalVisible = false
    },
    saveProfession(e) {
      this.state.profession = e
      this.professionModalVisible = false
    },
    editStat(id) {
      this.statModalVisible = true
      this.selectedStat = id
    },
    rollStat(id) {
      this.rollModalVisible = true
      this.selectedStat = id
    },
    saveStat(e) {
      if (e === 0) {
        this.state.statPool.push(this.state.stats[this.selectedStat])
        this.state.statPool.sort((a, b) => a-b)
      } else {
        const indexToRemove = this.state.statPool.findIndex((item) => item === e)
        this.state.statPool.splice(indexToRemove, 1)
      }
      this.$set( this.state.stats,this.selectedStat, e)
      this.statModalVisible = false
    },
    globalSave() {
      localStorage.setItem('state', JSON.stringify(this.state))
    },
    statBonus(id) {
      return (professionsDictionary[this.state.profession]?.bonuses[id] || 0)
    },
    rollValue(stat) {
      if (!stat) return 0
      const quirkBonus = this.state.quirks
        .filter(({inUse}) => inUse)
        .reduce((acc, cur) =>  acc + Number(cur.bonus), 0)
      console.log(quirkBonus);
      return this.formattedStats.find(({id}) => id === stat).value + this.fortuneBonus + quirkBonus
    },
    handleFortune(delta) {
      this.fortuneBonus += delta
      this.state.fortune -= delta
    },
    finishRoll() {
      this.rollModalVisible = false
      this.fortuneBonus = 0
    },
    resetFortune() {
      this.state.fortune = 13
      this.fortuneBonus = 0
    },
    addQuirk() {
      this.state.quirks.push({
        id: Math.random() * 1000000,
        name: "",
        bonus: 0
      })
    },
    removeQuirk(_id) {
      this.state.quirks = this.state.quirks.filter(({id}) => id !== _id)
    },
    showDescription(skill) {
      this.selectedSkill = skill
      this.skillDescriptionVisible = true
    }
  },
  computed: {
    formattedStats() {   
      return Object.entries(this.state.stats).map(([id, value]) => ({
        id,
        name: statDictionary[id],
        value: value + this.statBonus(id),
        inUse: false,
      }))
    },
    professionName() {
      return professionsDictionary[this.state.profession]?.name
    },
    skills() {
      const skills = []
      if (this.state.profession)
        skills.push(professionSkills[this.state.profession])
      this.formattedStats.forEach(({id, value}) => {
        if (value >= 5) skills.push(statSkills[id])
      })
      return skills
    }
  },

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.add-quirk-button {
  font-size: 14px; 
  width: 130px;
  margin: auto;
}
.global-save-button {
  margin: auto;
  margin-top: 20px;
  max-width: 300px;
}
.sheet {
  margin: 20px;
}
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
}

.grid-item {
  margin: 0 80px;
}

@media (max-width: 900px) {
  .grid { 
    grid-template-columns: 1fr;
  }

  .grid-item {
    margin: 0 ;
  }
}
table {
  width: 100%;
}
</style>
