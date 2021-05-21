<template>
   <div>
    <div class="months-navigation">
  Buscar por hashtags(#)
    </div>
   <div>
             <div class="form-group input-group">
            <span class="input-group-addon"><i class="glyphicon glyphicon-search"></i></span>
            <input name="consulta" id="txt_consulta" placeholder="Consultar" type="text" class="form-control">
        </div>
          <!--Google map-->
<div id="map-container-google-1" class="z-depth-1-half map-container" >
  <iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d3675.6031093877104!2d-47.108445584529136!3d-22.891115343068083!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x94c8c7c8b230a2cd%3A0x9b790ea60a817b70!2sR.%20Cardeal%20Joseph%20Cardjin%2C%20397%20-%20Jardim%20Eulina%2C%20Campinas%20-%20SP%2C%2013063-432!5e0!3m2!1spt-BR!2sbr!4v1621557515148!5m2!1spt-BR!2sbr" 
   frameborder="0" style="width: calc(100% - 10px); margin-bottom: -60px; height: 250px" allowfullscreen></iframe>
</div>
   </div>


<!--Google Maps-->
    <div class="container-group">
      <div class="container">
        <div v-if="activeMonth.data && !activeMonth.data.length">
          Você não cadastrou nenhum Tweet
        </div>
        <template v-else>
          <expense-list-item
            :key="index"
            :data="item"
            v-for="(item, index) in activeMonth.data"
          />
        </template>
      </div>
    </div>
    
  </div>
  
  
</template>

<script>
import moment from 'moment'
import groupBy from 'lodash.groupby'
import ExpenseListItem from '../expenses-list/ExpenseListItem'


export default {
  name: 'ExpensesList',
  components: {
    ExpenseListItem
  },
  data: () => ({
    expenses: [],
    activeMonth: {}
  }),
  created () {
    this.getData()
  },
  mounted () {
    this.setActiveMonth()
  },
  computed: {
    groupedMonths () {
      let groupedMonths = []

      const addCurrentMonth = () => {
        groupedMonths.push({
          data: [],
          total: 0,
          month: moment().format('MM/YYYY')
        })
      }

      if (this.expenses.length) {
        const months = groupBy(this.expenses, i => (
          moment(i.createdAt).format('MM/YYYY')
        ))

        const sortedMonths = Object.keys(months).sort((a, b) => {
          const pattern = 'MM/YYYY HH'

          return moment(`${a} 01`, pattern).isBefore(moment(`${b} 01`, pattern))
            ? -1
            : +1
        })

        groupedMonths = sortedMonths.map(month => ({
          month,
          data: months[month],
          total: months[month].map(i => +i.value).reduce((a, c) => a + c, 0)
        }))

        const lastMonth = moment(groupedMonths[groupedMonths.length - 1].month, 'MM/YYYY')

        if (!lastMonth.isSame(moment(), 'month')) {
          addCurrentMonth()
        }
      } else {
        addCurrentMonth()
      }

      return groupedMonths
    }
  },
  methods: {
    getData () {
      const ref = this.$firebase.database().ref(`/${window.uid}`)

      ref.on('value', snapshot => {
        const values = snapshot.val()
        this.expenses = Object.keys(values).map(i => values[i])
      })
    },
    setActiveMonth (month = null) {
      this.activeMonth = month || this.groupedMonths[this.groupedMonths.length - 1]
    }
    
  }
}
</script>

<style lang="scss" scoped>

 
.months-navigation {
  margin-left: -15px;
  background-color: var(--featured-dark);
  font-size: 20pt;
  text-align: center;
}

.form-control{
   margin-left: -15px;
}

.container-group {
margin-top: 70px;
    z-index: 1;
  .container {
    font-size: 15pt;
    position: fixed;
    
    left: 170;
  }
}

</style>
