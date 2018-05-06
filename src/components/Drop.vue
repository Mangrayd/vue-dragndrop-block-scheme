<template>
  <div>
    <div>{{activeArrStart}}</div>
    <div>{{activeArrEnd}}</div>

    <div class='gallery ui-helper-reset field' id="area"></div>

    <div id="gallery" class="gallery ui-helper-reset ui-helper-clearfix bar">

      <div class="ui-widget-content ui-corner-tr box box-start">
        <div class="box__remove delete"><span>x</span></div>
        <span class="ui-widget-header">Start El</span>
        <div class="box__out"></div>
      </div>
      <div class="ui-widget-content ui-corner-tr box box-cond">
        <div class="box__remove delete"><span>x</span></div>
        <span class="ui-widget-header">Cond El</span>
        <div class="box__in"></div>
        <div>
          <div class="box__out"></div>
          <div class="box__out"></div>
        </div>
      </div>
      <div class="ui-widget-content ui-corner-tr box box-input">
        <div class="box__remove delete"><span>x</span></div>
        <span class="ui-widget-header">Input El</span>
        <div class="box__in"></div>
        <div class="box__out"></div>
      </div>
      <div class="ui-widget-content ui-corner-tr box box-output">
        <div class="box__remove delete"><span>x</span></div>
        <span class="ui-widget-header">Output El</span>
        <div class="box__in"></div>
        <div class="box__out"></div>
      </div>
      <div class="ui-widget-content ui-corner-tr box box-finish">
        <div class="box__remove delete"><span>x</span></div>
        <span class="ui-widget-header">Finish El</span>
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
        coords: {x:null,y:null},
        activeArrStart: null,
        activeArrEnd: null
      }
    },
    mounted: function () {
      const self = this;
      const $gallery = $( "#gallery" );
      const $area   = $( "#area" );

      $.fn.extend({
        getPath: function () {
          var path, node = this;
          while (node.length) {
            var realNode = node[0], name = realNode.localName;
            if (!name) break;
            name = name.toLowerCase();

            var parent = node.parent();

            var sameTagSiblings = parent.children(name);
            if (sameTagSiblings.length > 1) {
              var allSiblings = parent.children();
              var index = allSiblings.index(realNode) + 1;
              if (index > 1) {
                name += ':nth-child(' + index + ')';
              }
            }

            path = name + (path ? '>' + path : '');
            node = parent;
          }

          return path;
        }
      });

      $( ".box", $gallery ).draggable({
        cancel: ".delete , .box__out",
        revert: "invalid",
        containment: "document",
        helper: "clone",
        cursor: "move",
        drag: function (event) {
          self.coords.x =event.pageX;
          self.coords.y =event.pageY;
        }
      });

      $area.droppable({
        accept: ".bar > .box",
        classes: {
          "ui-droppable-active": "ui-state-highlight"
        },
        drop: function( event, ui ) {
          deleteImage( ui.draggable );
        }
      });

      var arrowsDrawer2 = $cArrows('#area');

      function deleteImage( $item) {
        const $new_item = $item.clone();
            $new_item.draggable({
              cancel: ".delete , .box__out",
              containment: $area,
              cursor: "move",
              drag: (event) => {
                self.coords.x = event.clientX;
                self.coords.y = event.clientY;
                arrowsDrawer2.redraw();
              }
            });
            $new_item.css('position','absolute');
            $new_item.css('left',self.coords.x);
            $new_item.css('top',self.coords.y);

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
          let $item = $( this ),
            $target = $( event.target );

          if ( $target.is( ".delete" ) ) {
            $(this).remove();
            deleteImage( $item );
          }

          return false;
        });

    },
    methods: {}
  }
</script>

<style scoped>
  .field {
    height: 500px;
  }
  .bar {
    position: relative;
    display: flex;
    padding: 20px;
    border: 1px solid #000;
    justify-content: space-around;
    /*background-color: #2c3e50;*/
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
  .box__remove > span {
    margin: auto;
  }

  .box__in {
    position: absolute;
    cursor: pointer;
    background-color: red;
    height: 15px;
    width: 15px;
    top: calc(50% - 7.5px);
    left: -15px;
  }
  .box__out {
    position: absolute;
    cursor: pointer;
    background-color: red;
    height: 15px;
    width: 15px;
    top: calc(50% - 7.5px);
    right: -15px;
  }
  .box-cond .box__out:first-of-type {
    top: calc(33.33% - 7.5px);
  }
  .box-cond .box__out:last-of-type {
    top: calc(66.66% - 7.5px);
  }
</style>
