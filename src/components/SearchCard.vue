<template>
  <div class="container">
    <div class="logo-section">
      <img src="
https://cdn.discordapp.com/attachments/1004972309100114000/1140608286874406962/Yong_investment_financial_ratio_in_magnifier_stock_report_backg_ae769f51-ce01-4488-8aad-19cb82f8b815.png" id="company-logo" />
    </div>
    <div class="logo-text">
        <h2>Discover most related company to your keyword in 10 seconds</h2>
      </div>
      
    <div class="search-section">
    <div class="search-box">
        <input
            v-model="query"
            placeholder="Find related companies! Try 'Youtube'..."
            @keyup.enter="fetchData"
        />
        <button @click="fetchData">
             <svg
                xmlns="http://www.w3.org/2000/svg"
                width="30"
                height="30"
                viewBox="0 0 30 "
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
                class="feather feather-paper-plane"
             >
                <path d="M10 14l11 -11"></path>
                <path
                   d="M21 3l-6.5 18a.55 .55 0 0 1 -1 0l-3.5 -7l-7 -3.5a.55 .55 0 0 1 0 -1l18 -6.5"
                ></path>
             </svg>
        </button>
      </div>
</div>

    <div v-if="isLoading" class="skeleton">
      <div class="skeleton-header"></div>
      <div class="skeleton-body">
        <div class="skeleton-line"></div>
      </div>
      <div class="skeleton-body">
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
import mixpanel from 'mixpanel-browser';

mixpanel.init("e0e5a1748e7a2c12d17361e1c381a326");

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
    "model": "gpt-3.5-turbo",
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

        // Tracking the event
        mixpanel.track("Search Result", {
          "User Query": this.query,
          "Company Symbol": this.card.company_symbol,
          "Company Name": this.card.company_name,
          "Company Opinion": this.card.opinion
        });

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

.logo-section {
  display: flex;
  justify-content: center;
  width: 100%;
  margin-bottom: 1rem;
}

#company-logo {
  width: 150px;
  height: auto;
  border-radius: 100px;
}
.logo-text {
  margin-top: -20px;
  text-align: center;
}

.logo-text h2 {
  font-size: 20px;
  font-weight: bold;
}

.logo-text p {
  font-size: 15px;
  color: #555
}

.search-box {
  display: flex;
  justify-content: space-between;
  width: 100%;
  border: 1px solid #ddd;
  align-items: center;
  border-radius: 5px;
  padding: 10px;
  min-height: 44px;
  border: 1px solid #dfe1e5;
  border-radius: 40px;
  margin: 0 auto;
  max-width: 584px;
}

.search-box input {
  border: none;
  flex: 1;
  display: flex;
  flex-grow: 1;
  margin-right: 10px;
}

.search-box button {
  border: none;
  background: transparent;
  padding-right: 8px;
  padding-top: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 50px; /* updated button size */
  height: 50px; /* updated button size */

  svg {
    width: 30px; /* adjust SVG size */
    height: 30px; /* adjust SVG size */
  }
}

.search-box input:focus {
  output: none;
  box-shadow: 0 0 0 30px rgba(0, 123, 255, 0);
  min-height: 44px;
  line-height: 42px;
}

.search-section {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.nft-card {
  width: 100%;
  border: 1px solid #ddd;
  border-radius: 60px;
  padding: 0.5rem;
  margin-top: 1rem;
  margin-left: 1px;
  margin-right: 1px;
  text-align: center;
  max-width: 584px;
}

/* Skeleton styles */
.skeleton {
  width: 100%;
  border: 0px solid #ddd;
  border-radius: 100px;
  padding: 0.5rem;
  max-width: 584px;
  margin-top: 1rem;
  text-align: center;
  padding: 1rem;
  margin-top: 2rem;
  margin-left: 1px;
  margin-right: 1px;
}

.skeleton-header,
.skeleton-body {
  background: linear-gradient(-90deg, #f0f0f0 30%, #ddd 50%, #f0f0f0 70%);
  background-size: 400% 400%;
  animation: loading 1.2s ease-in-out infinite;
  border-radius: 60px;
}

.skeleton-header {
  height: 40px;
  margin-bottom: 24px;
  border-radius: 60px;
}

.skeleton-body .skeleton-line {
  height: 16px;
  margin-bottom: 16px;
  border-radius: 60px;
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
