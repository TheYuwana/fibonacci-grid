<template>
  <div class="grid">

    <div 
      v-for="block in this.blocks" 
      v-bind:key="block.key"
      v-on:click="addNumber(block.key)"
      :class="block.classes">
      <p>{{ block.number }}</p>
    </div>

  </div>
</template>

<script>
export default {
  name: 'Grid',
  data: function () {
    return {
      blocks: [],
      maxBlocks: 0,
      cOffset: 10
    }
  },
  created: function () {
    for(var i = 0; i < 100; i++){
      this.blocks[i] = {
        key: i,
        number: 0,
        classes: {
          'grid-block': true,
          'grid-block-highlighted': false
        }
      }
    }
    this.maxBlocks = this.blocks.length;
  },
  mounted: function(){
    // Automated clicks for the lol
    // setInterval(() => {
    //   this.addNumber(Math.floor(Math.random() * 100));
    // }, 750);
  },
  methods: {
    addNumber(key){

      // Adjust clicked block
      this.adjustBlock(key)

      // Get all columns below
      for(var i = (key+this.cOffset); i < this.maxBlocks; i += this.cOffset){
        if(i != key){
          this.adjustBlock(i)
        }
      }

      // Get all columns above
      for(var i = (key-this.cOffset); i >= 0; i -= this.cOffset){
        if(i != key){
          this.adjustBlock(i)
        }
      }

      // Adjust row
      // Split and get last digit
      let lastDigit = key;
      if(lastDigit >= 10){
        let digits = lastDigit.toString();
        lastDigit = parseInt(digits[digits.length-1], 10);
      }

      // Get min and max row values
      let minRow = key - lastDigit;
      let maxRow = key + (this.cOffset - (lastDigit+1))

      // Apply row numbers
      for(var i = minRow; i <= maxRow; i++){
        if(i != key){
          this.adjustBlock(i)
        }
      }

      // Fibonacci check
      let rowCount = 0;
      let rowBlocks = [];
      let fiboCelBlocks = [];

      // Check every row
      for(var i = 0; i < this.blocks.length; i+=10){
        
        minRow = rowCount * 10;
        maxRow = minRow + 9;
        rowCount++;
        
        rowBlocks = [];
        for(var k = minRow; k <= maxRow; k++){
          rowBlocks[k] = this.blocks[k];
        }
        rowBlocks = rowBlocks.filter(b => b != undefined);
        
        // Check the first five elements within a row
        for(var k = 0; k < rowBlocks.length; k++){
          if(k <= 5){
            
            fiboCelBlocks = [];
            for(var f = 0; f < 5; f++){
              fiboCelBlocks[f] = rowBlocks[k+f].number;
            }
            fiboCelBlocks = fiboCelBlocks.filter(b => b != undefined);

            if(this.fibonaccieArrayCheck(fiboCelBlocks)){
              for(var f = 0; f < 5; f++){
                this.fiboSequenced(rowBlocks[k+f].key);
              }
            }
          }
        }

      }
    },
    // Checks the array if all the digits contains the right Fibonacci digit and sequence
    fibonaccieArrayCheck(arrToCheck){
      let validCount = 0;
      for(var i = 0; i < arrToCheck.length; i++){
        if(this.fibonacciRangeCheck(arrToCheck[i])){
          if(i > 1 && arrToCheck[i] > 1){
            if(arrToCheck[i] == (arrToCheck[i-1] + arrToCheck[i-2])){
              validCount++;
            }else{
              break;
            }
          }
        }else{
          break;
        }
      }
      return validCount == 3;
    },
    // Check if a digit is withtin the Fibonacci sequence
    fibonacciRangeCheck(cel){
      let fabiArr = [0, 1];
      let fabiCount = 0;
      let valid = false;
      for(var i = 0; fabiCount <= cel; i++){
        if(i < 2){
          if(fabiArr[i] == cel){
            valid = true;
            break;
          }
        }else{
          fabiCount = fabiArr[fabiArr.length-1] + fabiArr[fabiArr.length-2]
          fabiArr.push(fabiCount)
          if(fabiCount == cel){
            valid = true;
            break;
          }
        }
      }
      return valid;
    },
    adjustBlock(key){
      let block = this.blocks[key];
      block.number += 1;
      block.classes = {
        'grid-block': true,
        'grid-block-highlighted': true,
        'grid-block-fiboSequenced': false
      };
      this.$set(this.blocks, key, block)

      // Remove highlighted after 0.5s
      setTimeout(() => {
        block.classes = {
          'grid-block': true,
          'grid-block-highlighted': false,
          'grid-block-fiboSequenced': false

        };
        this.$set(this.blocks, key, block)
      }, 500)
    },
    fiboSequenced(key){
      let block = this.blocks[key];
      block.classes = {
        'grid-block': true,
        'grid-block-highlighted': false,
        'grid-block-fiboSequenced': true
      };
      // Remove fiboSequenced after 0.5s
      setTimeout(() => {
        block.classes = {
          'grid-block': true,
          'grid-block-highlighted': false,
          'grid-block-fiboSequenced': false

        };
        block.number = 0;
        this.$set(this.blocks, key, block)
      }, 500)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

.grid {
  margin: 25px auto;
  width: 500px;
  display: flex;
  flex-wrap: wrap;

  &-block{
    width: 50px;
    height: 50px;
    border: 1px solid black;
    display: flex; 
    cursor: pointer;
    
    p{
      text-align: center;
      margin: auto;
    }

    &-highlighted{
      animation: highlighted 0.5s ease-in-out;
    }

    &-fiboSequenced{
      animation: fiboSequenced 0.5s ease-in-out;
    }
  }
}

@keyframes highlighted {
  0%   {background-color: white;}
  50%  {background-color: yellow;}
  100% {background-color: white;}
}

@keyframes fiboSequenced {
  0%   {background-color: white;}
  50%  {background-color: green;}
  100% {background-color: white;}
}

</style>
