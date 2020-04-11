<template>
    <ListView for="item in filteredItems">
        <v-template>
            <GroceryItem :groceryItem="item" @doneTap="onToggleDone" @nameTap="onItemTap" @deleteTap="onDeleteTap"></GroceryItem>
        </v-template>
    </ListView>
</template>

<script > 
    import GroceryItem from "./GroceryItem";
    import Detail from './Detail';
    

  export default {
    components: {GroceryItem, Detail},
    props: ['items','filterDone'],
    methods: {
        onToggleDone(groceryItem) {
            const newItem = Object.assign(groceryItem, { done: !groceryItem.done});

            const idx = this.items.findIndex(i => i.id === groceryItem.id);

            this.items = Object.assign( [], this.items, { idx: newItem});
        },
        onItemTap(args) {
            this.$navigateTo(Detail, {
                props: {
                    groceryItem: args
                },
                transitionAndroid: {
                    name:"slide",
                    duration: 300,
                    curve: "easeInOut"
                }
            });
        },
        onDeleteTap(groceryItem) {
            if(groceryItem.done){
                groceryItem.deleted=true;
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
