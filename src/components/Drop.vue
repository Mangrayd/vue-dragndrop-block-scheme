<template>
  <div>
    <div style="display: flex; justify-content: space-around">
      <div>количество элементов: {{generalElCount}}</div>
      <div>стартовый элемент: {{startElCount ? "yes" : "no"}}</div>
    </div>
    <div class="area" id="area"></div>
    <div class="bar" id="bar">

      <div class="box box-start" data-type="start">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Start El</div>
        <input class="box__input" type="text" value="start el text"/>
        <div class="box__out"></div>
      </div>

      <div class="box box-cond" data-type="cond">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Cond El</div>
        <input class="box__input" type="text" value="cond el if"/>
        <input class="box__input" type="text" value="cond el else"/>
        <div class="box__in"></div>
        <div>
          <div class="box__out box__out-first"></div>
        </div>
        <div>
          <div class="box__out box__out-last"></div>
        </div>
      </div>

      <div class="box box-input" data-type="input">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Input El</div>
        <input class="box__input" type="text" value="input el text"/>
        <div class="box__in"></div>
        <div class="box__out"></div>
      </div>

      <div class="box box-output" data-type="output">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Output El</div>
        <input class="box__input" type="text" value="output el text"/>
        <div class="box__in"></div>
        <div class="box__out"></div>
      </div>

      <div class="box box-finish" data-type="finish">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Finish El</div>
        <input class="box__input" type="text" value="finish el text"/>
        <div class="box__in"></div>
      </div>
    </div>

  </div>
</template>

<script>
  const interact = require('interactjs');

  export default {
    name: 'Drop',

    data () {
      return {
        coords: {
          x: null,
          y: null
        },
        startElCount: 0,
        startElMax: 1,
        generalElCount: 0,
        generalElMax: 10,
        activeArrStart: null,
        activeArrEnd: null,
        drawer: null,
        area: null,
        bar: null,
        elements: []
      }
    },
    mounted: function () {

      const self = this;
      this.bar    = $( "#bar" );
      this.area   = $( "#area" );
      this.drawer = $cArrows('#area');

      // START DRAG INITIALIZE
      $( ".box", this.bar ).draggable({
        cancel:      ".delete , .box__out",
        revert:      "invalid",
        containment: "document",
        helper:      "clone",
        cursor:      "move",
        drag: (event) => {
          self.coords.x = event.pageX;
          self.coords.y = event.pageY;
        }
      });

      // START DROP INITIALIZE
      this.area.droppable({
        accept:  ".bar > .box",
        classes: {
          "ui-droppable-active": "ui-state-highlight"
        },
        drop: ( event, ui ) => {
          self.cloneElement( ui.draggable );
        }
      });

    },
    methods: {
      cloneElement: function ($item) {
        const self = this;
        const $new_item = $item.clone();
              $new_item.draggable({
                cancel:      ".delete , .box__out, .box__input",
                containment: this.area,
                cursor:      "move",
                drag: (event) => {
                  self.coords.x = event.clientX;
                  self.coords.y = event.clientY;
                  self.drawer.redraw();
                }
              });
              $new_item.css('position','absolute');
              $new_item.css('left', self.coords.x);
              $new_item.css('top', self.coords.y);

              $new_item.appendTo( this.area );

              // SET ID
              let ms = (new Date).getTime();
              let type = $new_item[0].getAttribute('data-type');
              $new_item[0].setAttribute('data-id', ms);

              let newELObj = {
                type: type,
                id: ms,
              };

              if ( type === 'start' ) {
                this.startElCount++;
                this.generalElCount++;
              } else {
                this.generalElCount++;
              }
               if ( this.startElCount === this.startElMax ) {
                 $( ".box.box-start", this.bar ).addClass('disabled');
                 $( ".box.box-start", this.bar ).draggable({
                   disabled: true
                 });
               }

              if ( this.generalElCount === this.generalElMax ) {
                $( ".box:not(.box-start)", this.bar ).addClass('disabled');
                $( ".box:not(.box-start)", this.bar ).draggable({
                  disabled: true
                });
              }

              this.elements.push( {type: $new_item[0].getAttribute('data-type'),id: ms} );

              // this.elements
              $new_item.on( "click", ".delete", () => {

                if ( $new_item[0].getAttribute('data-type') === 'start' ) {
                  this.startElCount--;
                  this.generalElCount--;
                } else {
                  this.generalElCount--;
                }
                if ( this.startElCount !== this.startElMax ) {
                  $( ".box.box-start", this.bar ).removeClass('disabled');
                  $( ".box.box-start", this.bar ).draggable({
                    disabled: false
                  });
                }
                if ( this.generalElCount !== this.generalElMax ) {
                  $( ".box:not(.box-start)", this.bar ).removeClass('disabled');
                  $( ".box:not(.box-start)", this.bar ).draggable({
                    disabled: false
                  });
                }

                $new_item.remove();
                return false;
              });


        const listener1 = function (e) {
          console.log($(e.target));
          let id = $(e.target).parents('.box')[0].getAttribute('data-id');
          let newArr = [];
          $(e.target)[0].classList.forEach(function (item) {
            newArr.push('.'+item);
          });
          self.activeArrStart = '[data-id="' + id + '"] ' + newArr.join('');
        };
        const dbllistener1 = function (e) {
          let id = $(e.target).parents('.box')[0].getAttribute('data-id');
          let newArr = [];
          $(e.target)[0].classList.forEach(function (item) {
            newArr.push('.'+item);
          });

          let test  = '[data-id="' + id + '"] ' + newArr.join('');

          self.drawer.CanvasStorage[2].forEach(function (item,i) {
            if (item){
              if ( item[0] === test ){
                self.drawer.CanvasStorage[2].splice(i, 1);
              }
            }
          });
          self.drawer.redraw();
        };
        const boxOut = $new_item[0].querySelectorAll('.box__out');
              boxOut.forEach((item) =>{
                item.addEventListener('click', listener1);
                item.addEventListener('dblclick', dbllistener1, false);
              });

        const listener2 = function (e) {
          let id = $(e.target).parents('.box')[0].getAttribute('data-id');
          let newArr = [];
          $(e.target)[0].classList.forEach(function (item) {
            newArr.push('.'+item);
          });
          console.log(self.drawer.CanvasStorage[2]);
          self.activeArrEnd = '[data-id="' + id + '"] ' + newArr.join('');
          self.drawer.arrow(self.activeArrStart, self.activeArrEnd);


        };
        const boxIn = $new_item[0].querySelectorAll('#area .box__in');
              boxIn.forEach( (item) => {
                item.addEventListener('click', listener2)
              });
      }
    }
  }
</script>

<style scoped>
  .area {
    height: 500px;
    overflow: hidden;
  }
  .bar {
    position: relative;
    display: flex;
    padding: 20px;
    border: 1px solid #000;
    justify-content: space-around;
  }
  .box {
    position: relative;
    background-color: #fff;
    border-radius: 5px;
    border: 1px solid #d2d2d2;
    box-sizing: border-box;
    padding: 5px 10px;
    height: 100px;
    width: 100px;
  }
  .box.disabled {
    opacity: 0.5;
  }
  .box__remove {
    display: flex;
    position: absolute;
    cursor: pointer;
    background-color: antiquewhite;
    height: 15px;
    width: 15px;
    top: 0;
    left: 0;
    font-size: 12px;
  }
  .box__title {
    font-size: 14px;
    font-weight: 600;
    margin: 0 -10px;
    padding-bottom: 5px;
    border-bottom: 1px solid lightgrey;
  }
  .box__input {
    display: block;
    box-sizing: border-box;
    margin-top: 12px;
    margin-bottom: 30px;
    max-width: 100%;
  }
  .box__remove > span {
    margin: auto;
  }

  .box__in {
    position: absolute;
    cursor: pointer;
    background-color: #fff;
    border: 1px solid lightgrey;
    border-radius: 50%;
    height: 10px;
    width: 10px;
    top: calc(50% - 5px);
    left: -6px;
  }
  .box__out {
    position: absolute;
    cursor: pointer;
    background-color: #fff;
    border: 1px solid lightgrey;
    border-radius: 50%;
    height: 10px;
    width: 10px;
    top: calc(50% - 5px);
    right: -6px;
  }
  .box-cond {
    height: 150px;
  }
  .box-cond .box__out.box__out-first {
    top: calc(33.33% - 5px);
  }
  .box-cond .box__out.box__out-last {
    top: calc(66.66% - 5px);
  }
</style>
