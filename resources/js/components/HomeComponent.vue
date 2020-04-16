<template>
  <div class="row">
    <div class="col-12 bg-secondary" ref="table" style="height:400px;" @dragend="drop" @dragover="dragOver($event)">
      Table surface
      
      <div v-for="card in cardsOnTable" 
        style="position:absolute;" :style="cardStyle(card)" 
        class="d-flex justify-content-between border border-dark rounded p-1"
        draggable="true" @dragstart="dragStart(card, $event)" 
        @drag="dragging(card,$event)" :id="card.id">
        <template v-if="card.facingUp">
          <img src="/images/hearts.png" v-if="card.suit == 'hearts'" style="height:30px;">
          <img src="/images/clubs.png" v-if="card.suit == 'clubs'" style="height:30px;">
          <img src="/images/spades.png" v-if="card.suit == 'spades'" style="height:30px;">
          <img src="/images/diamonds.png" v-if="card.suit == 'diamonds'" style="height:30px;">
          <span>
            {{card.value}}
          </span>
        </template>
        <template v-if="currentCard.id == card.id">
          <img src="/images/flip.png" @click="flip(card)" style="position:absolute;bottom:3px;right:3px;width:15px;">
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
  function drag2(event){
    console.log("now dragging");
  }
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
        cardHeight:60,
        dragStartPos:{},
        currentCard:{}
      }
    },
    computed:{
      moveWidth(){
        return this.tableWidth - this.cardWidth;
      },
      moveHeight(){
        return this.tableHeight - this.cardHeight;
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
      dragStart(card, event){
        console.log("start of drag... "+card.value+ " of "+card.suit);
        console.log(event.clientX);
        this.dragStartPos = {
          x:event.clientX,
          y:event.clientY
        };
        this.currentCard = card;
      },
      dragging(card,event){
        //console.log("dragging x: "+event.pageX);
        //console.log("Dragging now...");
        //console.log(JSON.stringify(event.target));
      },
      dragOver(event){
        event.preventDefault();
        event.dataTransfer.dropEffect = "move";
      },
      drop(event){
        event.preventDefault();
        var id = event.target.id;
        console.log("id: "+event.target.id+", x: "+event.clientX);
        var deltaX = (event.clientX - this.dragStartPos.x) / this.moveWidth;
        var newX = this.currentCard.xPos + deltaX;
        if(newX < 0)
          newX = 0;
        if(newX>1)
          newX = 1;
        this.currentCard.xPos = newX;
        var deltaY = (event.clientY - this.dragStartPos.y) / this.moveHeight;
        var newY = this.currentCard.yPos + deltaY;
        if(newY < 0)
          newY = 0;
        if(newY>1)
          newY = 1;
        this.currentCard.yPos = newY;
        var index = -1;
        for(var i=0;i<this.cardsOnTable.length;i++){
          if(id == this.cardsOnTable[i].id)
            index = i;
        }
        if(index != -1){
          this.cardsOnTable.splice(index,1);
          this.cardsOnTable.push(this.currentCard);
        }
        
      },
      flip(card){
        console.log("going to try to flip the card with id: "+card.id);
        /*
        for(var i=0;i<this.cardsOnTable.length;i++){
          if(this.cardsOnTable[i].id == card.id)
            this.cardsOnTable[i].facingUp =
        }*/
        card.facingUp = !card.facingUp;
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
        var serialNum = 0;
        for(var i=0;i<4;i++){
          for(var j=0;j<13;j++){
            this.cardsOnTable.push({
              suit:this.suits[i],
              value:this.values[j],
              xPos:0.5,
              yPos:0.5,
              facingUp:false,
              id:serialNum
            });
            serialNum++;
          }
        }
        this.shuffleCards();
        this.tableWidth = this.$refs.table.clientWidth;
      },
      
    }
  }
</script>