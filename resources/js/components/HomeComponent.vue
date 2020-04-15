<template>
  <div class="row">
    <div class="col-12 bg-secondary" ref="table" style="height:400px;">
      Table surface
      
      <div v-for="card in cardsOnTable" 
        style="position:absolute;" :style="cardStyle(card)" 
        class="d-flex justify-content-between border border-dark rounded p-1">
        <template v-if="card.facingUp">
        <img src="/images/hearts.png" v-if="card.suit == 'hearts'">
        <img src="/images/clubs.png" v-if="card.suit == 'clubs'">
        <img src="/images/spades.png" v-if="card.suit == 'spades'">
        <img src="/images/diamonds.png" v-if="card.suit == 'diamonds'">
          <span>
            {{card.value}}
          </span>
        </template>
      </div>
    </div>
    <div class="fixed-bottom border border-secondary">
      <div class="form-group">
        <label>Name</label>
        <input type="text" v-model="username" class="form-control">
      </div>
      <div class="btn btn-primary" @click="addPlayer()">Add player</div>
      <div class="btn btn-primary" v-if="players.length>0" @click="start()">Start</div>
    </div>
  </div>
</template>

<script>
  export default{
    data(){
      return{
        message:"Hi",
        username:'',
        players:[],
        cardsOnTable:[],
        suits:[
          "hearts",
          "clubs",
          "spades",
          "diamonds"
        ],
        values:[
          "A","2","3","4","5","6","7","8","9","10","J","Q","K"
        ],
        tableWidth:'',
        tableHeight:400,
        cardWidth:60,
        cardHeight:60
      }
    },
    methods:{
      addPlayer(){
        this.players.push({
          username:this.username,
          cards:[]
        });
      },
      cardStyle(card){
        var fillColor = '#aaa';
        if(card.facingUp)
          fillColor = '#fff';
        
        return {
          'background-color':fillColor,
          'width':this.cardWidth+'px',
          'height':this.cardHeight+'px',
          'top':((this.tableHeight-this.cardHeight) * card.yPos)+'px',
          'left':((this.tableWidth-this.cardWidth) * card.xPos)+'px'
        }
      },
      shuffleCards(){
        var temp = this.cardsOnTable;
        this.cardsOnTable = [];
        var iterations = temp.length;
        for(var i=0;i<iterations;i++){
          var index = Math.floor(Math.random() * temp.length);
          this.cardsOnTable.push(temp.splice(index,1)[0]);
        }
      },
      start(){
        //alert("Should start now");
        
        for(var i=0;i<4;i++){
          for(var j=0;j<13;j++){
            this.cardsOnTable.push({
              suit:this.suits[i],
              value:this.values[j],
              xPos:0.5,
              yPos:0.5,
              facingUp:false
            });
          }
        }
        this.shuffleCards();
        this.tableWidth = this.$refs.table.clientWidth;
      },
      
    }
  }
</script>