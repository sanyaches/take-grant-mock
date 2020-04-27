<template>
  <div class="take-grant-page">
    <network
      ref="network"
      :nodes="nodes"
      :edges="edges"
      :options="options"
      :events="['selectNode', 'doubleClick']"
      @select-node="onSelectNode"
      @double-click="onDoubleClickNode"
    />
    <div class="Information">
      <p> First selected node: {{selectedNode.first.label || 'Not selected.'}} </p>
      <p> Second selected node: {{selectedNode.second.label || 'Not selected.'}} </p>
      <p> Available rights: {{ selectedEdge.label || 'Not selected.'}} </p>
    </div>
    <div class="buttons">
      <button @click="startAction('take')" class="button" :disabled="!getRights.take">Take</button>
      <button @click="startAction('grant')" class="button" :disabled="!getRights.grant">Grant</button>
      <button @click="startAction('create')" class="button" :disabled="!getRights.create">Create</button>
      <button @click="startAction('remove')" class="button" :disabled="!getRights.remove">Remove</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      selectedNode: {
        first: {},
        second: {}
      },
      selectedEdge: {},
      nodes: [
        {id: 1, label: 'Subject1'},
        {id: 2, label: 'Subject2'},
        {id: 3, label: 'Subject3'},
        {id: 4, label: 'Subject4'},
        {id: 5, label: 'Subject5'},
        {id: 6, label: 'Object1'},
        {id: 7, label: 'Object2'},
        {id: 8, label: 'Object3'},
        {id: 9, label: 'Object4'},
        {id: 10, label: 'Object5'}
      ],
      edges: [
        {from: 1, to: 6, label: 't'},
        {from: 1, to: 7, label: 'g,r'},
        {from: 1, to: 8, label: 't'},
        {from: 1, to: 9, label: 't,c'},
        {from: 1, to: 10, label: 'g,r,c'},

        {from: 2, to: 6, label: 't,r'},
        {from: 2, to: 7, label: 'g'},
        {from: 2, to: 8, label: 't,c'},
        {from: 2, to: 9, label: 't,c'},
        {from: 2, to: 10, label: 'g,r,c'},

        {from: 3, to: 6, label: 't,g'},
        {from: 3, to: 7, label: 'g,c'},
        {from: 3, to: 8, label: 't'},
        {from: 3, to: 9, label: 't,c'},
        {from: 3, to: 10, label: 'g,r,c'},

        {from: 4, to: 6, label: 't'},
        {from: 4, to: 7, label: 'g'},
        {from: 4, to: 8, label: 't'},
        {from: 4, to: 9, label: 't,r'},
        {from: 4, to: 10, label: 'g,r,c'},

        {from: 5, to: 6, label: 't,r'},
        {from: 5, to: 7, label: 'g'},
        {from: 5, to: 8, label: 't,c'},
        {from: 5, to: 9, label: 't,c'},
        {from: 5, to: 10, label: 'g,r,c'}
      ],
      options: {
        nodes: {
          borderWidth: 3,
          color: '#f2f2f2'
        },
        edges: {
          color: 'lightgray'
        },
        width: '100%',
        height: '600'
      }
    }
  },
  computed: {
    getRights () {
      if (typeof this.selectedEdge.label === 'undefined') {
        return {
          take: false,
          grant: false,
          create: false,
          remove: false
        }
      }

      const canTake = this.selectedEdge.label.includes('t');
      const canGrant = this.selectedEdge.label.includes('g');
      const canRemove = this.selectedEdge.label.includes('r');
      const canCreate = this.selectedEdge.label.includes('c');

      return {
        take: canTake || false,
        grant: canGrant || false,
        create: canCreate || false,
        remove: canRemove || false
      }
    }
  },
  methods: {
    onDoubleClickNode (node) {
      const indexNode = node.nodes[0];

      if (indexNode === this.selectedNode.index) {
        this.$refs.network.unselectAll();

        this.deselectNodes();

        return null
      }
    },
    onSelectNode (node) {
      const indexNode = node.nodes[0]
      const existSelect = typeof this.selectedNode.first.label !== 'undefined';
      const secondExist = typeof this.selectedNode.second.label !== 'undefined';
      const selectedNode = {
        index: this.nodes[indexNode - 1].id,
        label: this.nodes[indexNode - 1].label
      };

      if (!existSelect) {
        if (indexNode && indexNode <= this.nodes.length) {
          this.selectedNode.first = selectedNode
        } else {
          throw new Error('WHat are hell? Selected node is not exist in graph or undefined!')
        }
      } else {
        if (!secondExist) {
          this.selectedNode.second = selectedNode;
          this.findRights();
        } else {
          this.deselectNodes();
          this.selectedNode.first = selectedNode
        }
      }
    },
    findRights () {
      if (this.selectedNode.first.index >= this.selectedNode.second.index) throw new Error('NONONO! Subject before Object!');
      if (typeof this.selectedEdge.label !== 'undefined') this.selectedEdge = {};

      this.selectedEdge = this.edges[(this.selectedNode.first.index - 1) * 5 + (this.selectedNode.second.index - 6)];
    },
    startAction (action) {
      console.log(`${this.selectedNode.first.label} want to "${action}" with ${this.selectedNode.second.label}`);
      this.deselectNodes();
    },
    deselectNodes () {
      this.selectedNode.first = {};
      this.selectedNode.second = {};
      this.selectedEdge = {}
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.take-grant-page {
  height: 90vh;
}

.buttons {
  display: flex;
  justify-content: center;
  padding: 20px;
}
.button {
  margin: 0 20px;
  font-size: 30px;
}
</style>
