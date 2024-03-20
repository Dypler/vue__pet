<script setup>
    import { reactive, inject, watch, ref, onMounted} from 'vue';
    import axios from 'axios';
    import debounce from 'lodash.debounce';
    import CardList from '../components/CardList.vue'

    const { cart, addToCart, removeFromCart } = inject('cart');

    const items = ref([]);

    const filters = reactive ({
    searchQuery: '',
    sortBy: 'title'
    });
    
    const onClickAddPlus = (item) => {
    if (!item.isAdded) {
        addToCart(item);
    } else {
        removeFromCart(item);
    }
    console.log(cart);
    }
    const onChangeSelect = event => {
    filters.sortBy = event.target.value
    }
    const onChangeSearchInput = debounce((event) => {
    filters.searchQuery = event.target.value
    }, 500)
    const addToFavorite = async (item) => {
    try {

      if (!item.isFavorite) {
        const obj = {
        item_id: item.id
        }
        item.isFavorite = true
        const {data} = await axios.post(`https://1dba12d59af42e98.mokky.dev/favorites`, obj);
        item.favoriteId = data.id;
      } else {
        item.isFavorite = false
        await axios.delete(`https://1dba12d59af42e98.mokky.dev/favorites/${item.favoriteId}`);
        item.favoriteId = null;

      }
    } catch (error) {
      console.log(error);
    }
}
const fetchFavorite = async () => {
  try {
    const {data: favorites} = await axios.get(`https://1dba12d59af42e98.mokky.dev/favorites`)
    items.value = items.value.map(item => {
      const favorite = favorites.some(favorite => favorite.item_id === item.id);
      if (!favorite) {
        return item;
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      }
    });
    console.log(items);
  } catch (error) {
    console.log(error);
  }
}

    const fetchItems = async () => {
    try {
        const params = {
        sortBy: filters.sortBy,
        // searchQuery: filters.searchQuery
        }
        if (filters.searchQuery) {
        params.title = `*${filters.searchQuery}*`
        }
        const {data} = await axios.get(`https://1dba12d59af42e98.mokky.dev/items`, {
        params
        })   
        items.value = data.map((obj) => ({
        ...obj,
        isFavorite: false,
        favoriteId: null,
        isAdded: false
        })) ;
    } catch (error) {
        console.log(error);
    }
    }

    onMounted (async () => {
  
    const localCart = localStorage.getItem('cart');
    cart.value = localCart ? JSON.parse(localCart) : [];
    

    await fetchItems();
    await fetchFavorite();

    items.value = items.value.map((item) => ({
        ...item,
        isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
    }))
    })

    watch(cart, () => {
        items.value = items.value.map((item) => ({
            ...item,
            isAdded: false
        }))
    })

    watch(filters,fetchItems)
</script>

<template>
    <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-10">Все кроссовки</h2>
        <div class="flex gap-4">
          <select @change="onChangeSelect" class="py-2 px-2 border border-md outline-none"  name="" id="">
          <option value="name">По названию</option>
          <option value="price">По цене (дешевые)</option>
          <option value="-price">По цене (дорогие)</option>

        </select>
        <div class="relative">
          <img class="absolute left-4 top-3" src="/search.svg" >
          <input 
            @input="onChangeSearchInput"
            class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
            type="search" 
            name="" 
            id=""
            placeholder="Поиск...">
        </div>
        </div>
      </div>
      <div class="mt-10"> 
        <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus"/>
      </div>
</template>