<template>
  <div id="app">
    <Drop/>
  </div>
</template>

<script>

import Drop from './components/Drop'

export default {
  name: 'App',
  components: {
    Drop
  },
  mounted: function () {
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
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
