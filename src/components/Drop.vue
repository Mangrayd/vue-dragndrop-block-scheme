<template>
  <div>

    <div class="area" id="area"></div>
    <div class="bar" id="bar">

      <div class="box box-start">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Start El</div>
        <div class="box__out"></div>
      </div>

      <div class="box box-cond">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Cond El</div>
        <div class="box__in"></div>
        <div>
          <div class="box__out"></div>
          <div class="box__out"></div>
        </div>
      </div>

      <div class="box box-input">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Input El</div>
        <div class="box__in"></div>
        <div class="box__out"></div>
      </div>

      <div class="box box-output">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Output El</div>
        <div class="box__in"></div>
        <div class="box__out"></div>
      </div>

      <div class="box box-finish">
        <div class="box__remove delete"><span>x</span></div>
        <div class="box__title">Finish El</div>
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
        activeArrStart: null,
        activeArrEnd: null
      }
    },
    mounted: function () {
      const self = this;
      const bar = $( "#bar" );
      const $area   = $( "#area" );

      $( ".box", bar ).draggable({
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

      $area.droppable({
        accept:  ".bar > .box",
        classes: {
          "ui-droppable-active": "ui-state-highlight"
        },
        drop: ( event, ui ) => {
          cloneElement( ui.draggable );
        }
      });

      const arrowsDrawer2 = $cArrows('#area');

      function cloneElement( $item) {
        const $new_item = $item.clone();
              $new_item.draggable({
                cancel:      ".delete , .box__out",
                containment: $area,
                cursor:      "move",
                drag: (event) => {
                  self.coords.x = event.clientX;
                  self.coords.y = event.clientY;
                  arrowsDrawer2.redraw();
                }
              });
              $new_item.css('position','absolute');
              $new_item.css('left', self.coords.x);
              $new_item.css('top', self.coords.y);

              $new_item.appendTo( $area );
              $new_item.on( "click",".delete", () => {
                $new_item.remove();
                return false;
              });

        const boxOut = document.querySelectorAll('.box__out');
              boxOut.forEach((item) =>{
                item.addEventListener('click', (e) => {
                  self.activeArrStart = $(e.target).getPath();
                })
              });

        const boxIn = document.querySelectorAll('.box__in');
              boxIn.forEach( (item) => {
                item.addEventListener('click', (e) => {
                  self.activeArrEnd = $(e.target).getPath();
                  arrowsDrawer2.arrow(self.activeArrStart, self.activeArrEnd);
                })
              });
      }

      $( ".bar > .box" ).on( "click", ( event ) => {
        if ( $( event.target ).is( ".delete" ) ) {
          $(this).remove();
        }
        return false;
      });

    },
    methods: {}
  }
</script>

<style scoped>
  .area {
    height: 500px;
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
    background-color: #42b983;
    box-sizing: border-box;
    padding: 10px;
    height: 100px;
    width: 100px;
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
    left: -5px;
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
    right: -5px;
  }
  .box-cond .box__out:first-of-type {
    top: calc(33.33% - 5px);
  }
  .box-cond .box__out:last-of-type {
    top: calc(66.66% - 5px);
  }
</style>
