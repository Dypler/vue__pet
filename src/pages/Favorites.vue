<script setup>
    import {  ref, onMounted} from 'vue';
    import axios from 'axios';

    import CardList from '../components/CardList.vue';

    const favorites = ref([]);

    onMounted(async () => {
        try {
            const {data} = await axios.get('https://1dba12d59af42e98.mokky.dev/favorites?_relations=items');  
            favorites.value = data.map((obj) => obj.item); ; 
        } catch (error) {
            console.log(error);
        }
    });
</script>

<template>  
    <h2 class="text-3xl font-bold mb-10" v-auto-animate>Мои закладки</h2>
    <CardList :items="favorites" is-favorites />
</template>