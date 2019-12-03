<template>
  <div id="app">
    <div class="container" :style="containerStyle">
      <div v-for="cell in cells" :key="cell.id" class="cell" :class="cell.class">
        {{cell.name}}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data: () => ({
    // Dummy Application FactSheet Array
    applications: [...Array(10).keys()].map(key => ({ id: `application-${key}`, name: `Application ${key}` })),
    // Dummy Business Capabilities Array
    businessCapabilities: [...Array(10).keys()].map(key => ({ id: key, name: `Business Capability ${key}` }))
  }),
  computed: {
    // Computed variable used to dynamicall set the number of columns of the grid container
    containerStyle () {
      const firstColumnWidth = `120px`
      const columnWidth = `120px`
      return `grid-template-columns: ${firstColumnWidth} repeat(${this.businessCapabilities.length}, ${columnWidth});`
    },
    cells () {
      // Computed array for the cells, function of Application and Business Capability Arrays
      const cells = this.applications
        .reduce((accumulator, application, i) => {
          if (i === 0) {
            const firstRow = [{ class: 'pivot-cell', id: `pivot-cell` }, ...this.businessCapabilities.map((businessCapability, j) => ({ ...businessCapability, id: `col-${j}`, class: `business-capability-header` }))]
            accumulator = [ ...accumulator, ...firstRow ]
          }
          const firstCell = { ...application, class: `application-header` }
          const row = [firstCell, ...this.businessCapabilities
            .map((businessCapability, j, businessCapabilities) => {
              // above we compute the cell value, given a certain aplication and business capability
              const cellIndex = i * businessCapabilities.length + j
              const computedCellValue = { id: cellIndex, name: `Cell ${cellIndex}\n${application.name}\n${businessCapability.name}` }
              return computedCellValue
            })]
          return [ ...accumulator, ...row ]
        }, [])
      return cells
    }
  },
  created () {
    this.$lx.init()
      .then(setup => this.$lx.ready({}))
  }
}
</script>

<style lang="stylus">
body
  margin 0

#app
  font-family 'Avenir', Helvetica, Arial, sans-serif
  -webkit-font-smoothing antialiased
  -moz-osx-font-smoothing grayscale
  font-size 12px
  color #2c3e50
  height 100vh
  display flex
  justify-content center
  align-items center

.container
  display grid
  .cell
    white-space pre-line // preserve line-breaks inside the cells
    margin 2px
    padding 0.5rem
    border-radius 3px
    display flex
    align-items center
    justify-content center
    border 1px solid black
  .application-header
    background red
    color white
  .business-capability-header
    background green
    color white
  .pivot-cell
    border none
</style>
