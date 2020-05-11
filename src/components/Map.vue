<template>
  <div id="map">
    <canvas width="500" height="500" class="canvas" ref="canvas"></canvas>
    <div id="buttons">
    <button type="button" class="btn btn-primary" @click="fetchCell(0)">↑</button>
    <button type="button" class="btn btn-primary" @click="fetchCell(1)">→</button>
    <button type="button" class="btn btn-primary" @click="fetchCell(2)">↓</button>
    <button type="button" class="btn btn-primary" @click="fetchCell(3)">←</button>
    </div>
    <p>CurrentID is {{ currentId }}</p>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  props: {},
  data() {
    return {
      cell: {},
      currentId: 0,
    }
  },
  methods: {
    fetchCell: function(direction) {
      this.currentId = this.cell.neighbors[direction].id
      axios
        .get('http://localhost:8080/map/' + this.cell.neighbors[direction].id)
        .then(response => {
          this.cell = response.data
        })
        .catch(function (err) {
          console.log(err);
        });
    },
    draw() {
      var id2Color = function(id) {
        var type = id%12
        if (type == 0) {
          return "lightblue"
        } else if (type == 1 ){
          return "mediumaquamarine"
        }else if (type==2) {
          return "mediumseagreen"
        }else if (type == 3) {
          return "mediumturquoise"
        } else if (type == 4) {
          return "turquoise"
        } else if (type == 5) {
          return "mediumspringgreen"
        } else if (type == 6) {
          return "royalblue"
        } else if (type == 7) {
          return "lightcyan"
        } else if (type == 8) {
          return "cadetblue"
        } else if (type == 9) {
          return "paleturquoise"
        } else if (type == 10) {
          return "seagreen"
        } else {
          return "springgreen"
        }
      }
      this.ctx.beginPath()
      this.ctx.clearRect(0, 0, 500, 500)
      // マス目描画
      var img = new Image()
      img.src = "../assets/logo.png"
      this.ctx.drawImage(img, 200, 200, 100, 100)
      this.ctx.fillStyle = id2Color(this.cell.center.id)
      this.ctx.fillRect(200,200,100,100) // center
      this.ctx.fillStyle = id2Color(this.cell.neighbors[0].id)
      this.ctx.fillRect(200,100,100,100) // top
      this.ctx.fillStyle = id2Color(this.cell.neighbors[1].id)
      this.ctx.fillRect(300,200,100,100) // right
      this.ctx.fillStyle = id2Color(this.cell.neighbors[2].id)
      this.ctx.fillRect(200,300,100,100) //bottom
      this.ctx.fillStyle = id2Color(this.cell.neighbors[3].id)
      this.ctx.fillRect(100,200,100,100) // left

      this.ctx.fillStyle = id2Color(this.cell.indirects[0].id)
      this.ctx.fillRect(200,0,100,100) // top
      this.ctx.fillStyle = id2Color(this.cell.indirects[1].id)
      this.ctx.fillRect(400,200,100,100) // right
      this.ctx.fillStyle = id2Color(this.cell.indirects[2].id)
      this.ctx.fillRect(200,400,100,100) //bottom
      this.ctx.fillStyle = id2Color(this.cell.indirects[3].id)
      this.ctx.fillRect(0,200,100,100) // left
      this.ctx.fillStyle = id2Color(this.cell.indirects[4].id)
      this.ctx.fillRect(300,100,100,100) // top right
      this.ctx.fillStyle = id2Color(this.cell.indirects[5].id)
      this.ctx.fillRect(300,300,100,100) // bottom right
      this.ctx.fillStyle = id2Color(this.cell.indirects[6].id)
      this.ctx.fillRect(100,300,100,100) //bottom left
      this.ctx.fillStyle = id2Color(this.cell.indirects[7].id)
      this.ctx.fillRect(100,100,100,100) //top left

      this.ctx.fill()
    }
  },
  mounted () {
    axios
      .get('http://localhost:8080/map') // init
      .then(response => {
        console.log(response.data)
      })
    axios
      .get('http://localhost:8080/map/0')
      .then(response => {
        this.cell = response.data
        this.ctx = this.$refs.canvas.getContext('2d')
        this.draw()
      })
  },
  watch: {
    cell() {
      this.draw()
    }
  }
}
</script>

<style scoped>
.canvas {
  border: 1px solid #000;
}
</style>
