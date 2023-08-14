<template>
  <div class="container">
    <div class="search-section">
      <div class="search-box">
  <input v-model="query" placeholder="Find related companies! Try 'Youtube'..." />
  <button @click="fetchData">
    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-paper-plane">
      <path d="M10 14l11 -11"></path>
      <path d="M21 3l-6.5 18a.55 .55 0 0 1 -1 0l-3.5 -7l-7 -3.5a.55 .55 0 0 1 0 -1l18 -6.5"></path>
    </svg>
   </button>
</div>
    </div>

    <div v-if="isLoading" class="skeleton">
    <div class="skeleton-header"></div>
    <div class="skeleton-body">
      <div class="skeleton-line"></div>
      <div class="skeleton-line"></div>
      <div class="skeleton-line"></div>
    </div>
  </div>

    <div v-if="card" class="nft-card">
      <!-- <img :src="card.logo_url" alt="Card symbol image" /> -->
      <h2>{{ card.company_symbol }}</h2>
      <h3>{{ card.company_name }}</h3>
      <p>{{ card.opinion }}</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      query: '',
      card: null,
      isLoading: false
    };
  },
  methods: {
    async fetchData() {
    this.isLoading = true;
        const response = await axios.post('https://api.openai.com/v1/chat/completions', {
    "model": "gpt-4",
    "messages": [
      {
        "role": "system",
        "content": "You are a helpful API that gets user input to find the most related public company and then answer as a symbol. The answer pattern is like API style by this template {'company_symbol':'AAPL', 'company_name':'Apple, Inc.', 'opinion':'Because ....'} p.s. it's okay if it's not directly related but try to answer as per the template, p.s.1 if you can't find an answer response {'company_symbol':'AAPL', 'company_name':'Apple, Inc.'} else answer {'company_symbol':'None', 'company_name':'None', 'opinion':'I cannot find related symbol'}."
      },
      {
        "role": "user",
        "content": `What is the company that is the most related to : '${this.query}'`
      }
    ]
  }, {
            headers: { 'Authorization': 'Bearer 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' }
        }).finally(() => {
            this.isLoading = false;
        });

        const content = response.data['choices'][0]['message']['content'];
        let result = null;
        try {
            result = JSON.parse(content.replace(/'/g, '"'));
        } catch {
            console.log("Failed to parse the message content to JSON");
            return;
        }

        const logoUrl = `https://logo.clearbit.com/${result['company_symbol']}.com`;
        result['logo_url'] = logoUrl.replace(" ", "");
        
        this.card = result;
    }
},
};
</script>


<style scoped lang="scss">
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 1rem;

  @media (max-width: 768px) {
    padding: 0.5rem;
  }
}

.search-box {
  display: flex;
  justify-content: space-between;
  width: 100%;
  border: 1px solid #ddd; 
  align-items: center;
  border-radius: 5px;
  padding: 10px;
}


.search-box input {
  border: none;
  flex-grow: 1;
  margin-right: 10px;
}

.search-box button {
  border: none;
  background: transparent;
  padding: 0;
}

.search-box input:focus {
  output: none;
}

.search-section {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

input {
  height: 100%;
  flex-grow: 1;
  margin-right: 0.5rem;
}

button {
  align-self: flex-end;
  background: transparent;
  border: none;
}

.nft-card {
  width: 100%;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1rem;
  margin-top: 1rem;
  margin-left: 1px;
  margin-right: 1px;
  text-align: center;

  @media (max-width: 768px) {
    width: 100%;
    padding: 0.5rem;
    margin-top: 0.5rem;
  }
}

/* Add skeleton styles */
.skeleton {
  width: 100%;
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 1rem;
  margin-top: 1rem;
  text-align: center;

  @media (max-width: 768px) {
    width: 100%;
    padding: 0.5rem;
    margin-top: 0.5rem;
  }
}

.skeleton-header, .skeleton-body {
  background: linear-gradient(-90deg, #f0f0f0 30%, #ddd 50%, #f0f0f0 70%);
  background-size: 400% 400%;
  animation: loading 1.2s ease-in-out infinite;
}

.skeleton-header {
  height: 40px;
  margin-bottom: 24px;
}

.skeleton-body .skeleton-line {
  height: 16px;
  margin-bottom: 16px;
  border-radius: 4px;
}

@keyframes loading {
  0% {
    background-position: 100% 0;
  }
  100% {
    background-position: -100% 0;
  }
}
</style>