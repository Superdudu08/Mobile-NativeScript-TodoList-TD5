<template>
    <ListView for="item in filteredItems">
        <v-template>
            <TodoItem :todoItem="item" @doneTap="onToggleDone" @nameTap="onItemTap" @deleteTap="onDeleteTap"></TodoItem>
        </v-template>
    </ListView>
</template>

<script > 
    import TodoItem from "./TodoItem";
    import Detail from './Detail';
    

  export default {
    components: {TodoItem, Detail},
    props: ['items','filterDone'],
    methods: {
        onToggleDone(todoItem) {
            const newItem = Object.assign(todoItem, { done: !todoItem.done});

            const idx = this.items.findIndex(i => i.id === todoItem.id);

            this.items = Object.assign( [], this.items, { idx: newItem});
        },
        onItemTap(args) {
            this.$navigateTo(Detail, {
                props: {
                    todoItem: args
                },
                transitionAndroid: {
                    name:"slide",
                    duration: 300,
                    curve: "easeInOut"
                }
            });
        },
        onDeleteTap(todoItem) {
            if(todoItem.done){
                todoItem.deleted=true;
                this.$emit('updateItems');
            }
        }
    },
    computed: {
        filteredItems: function() {
            this.$emit('updateItems');
            if (this.filterDone){
                
                return this.items.filter((item) => !item.done && !item.deleted);
            }
            return this.items.filter((item) => !item.deleted);
        }
    }
  }
</script>

<style lang="scss" scoped>
    
</style>
