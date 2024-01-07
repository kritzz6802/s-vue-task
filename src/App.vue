<script setup>
import {ref,watch} from 'vue';
import arrow from './assets/icons-arrow.png'

  const characters = ref([]);
  const searchInput = ref('');

  const fetchData = async () => {
    try {
      const response = await fetch(
        `https://api.jikan.moe/v4/characters?page=1&limit=15&q=${searchInput.value}&order_by=favorites&sort=desc`
      );
      const data = await response.json();
      characters.value=data.data
    } catch (error) {
      console.error("Error at fetching data:", error);
    }
  };
  fetchData()

  watch(searchInput, ()=>{
    if(searchInput.value===''){
      fetchData()
    }
  });

  const handleSearch = () => {
    fetchData();
  };
</script>

<template>
     <div>
      <div className="upper-part">
        <h1>Character Search</h1>
        <div>
          <input
            className="search-input"
            type="text"
            v-model="searchInput"
            @change="handleInputChange"
            placeholder="Search characters"
          />
          <button className="search-button" @click='handleSearch' >
            Search
          </button>
        </div>
        <p className="total-matching">
          Total {{characters.length}} Matching Character Name
        </p>
      </div>
      <div className="main-container">
          <div className="container">
              <div v-for="character in characters" className="character-container" :key="character.mal_id">
                <img
                  className="character-image"
                  :src="character.images?.jpg?.image_url"
                  alt=""
                />
                <div className="character-details">
                  <div>
                    <p>{{character.name}}</p>
                    <div className="nickname-chips">
                        <span v-for="nickname in character.nicknames" :key="nickname" className="nickname-chip">
                          {{nickname}}
                        </span>
                    </div>
                  </div>
                  <div className="right-part">
                    <div className="favorite-part">
                      <span className="favorite-icon">❤️</span> {{character.favorites}}
                    </div>
                    <div className="url-section">
                      <a
                        :href="character.url"
                        target="_blank"
                        rel="noopener noreferrer"
                        className="link-icon"
                      >
                        <img
                          :src='arrow'
                          alt=""
                        />
                      </a>
                    </div>
                  </div>
                </div>
              </div>
          </div>
          <p v-if="characters.length===0" className="no-data">No Result Found</p>
      </div>
    </div>
</template>