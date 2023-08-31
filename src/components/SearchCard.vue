<template>
  <div class="container">
    <div class="logo-section">
      <img
        data-v-f149e288=""
        src="mascot.png"
        alt="Discover related stock using your keywords in just a few seconds."
        id="company-logo"
        width="150"
        height="150"
      />
    </div>
    <div class="search-section">
      <div class="search-box">
        <button @click="fetchData" aria-label="Send data">
        <!--
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="25"
            height="25"
            viewBox="0 0 25 25"
            fill="none"
            stroke="#555555"
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
          -->
          <svg aria-hidden="true" width="25" height="24" viewBox="0 0 25 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M10.4167 0C15.7555 0 20.0833 4.32792 20.0833 9.66667C20.0833 11.9759 19.2736 14.0959 17.9227 15.7584L24.4571 22.2929C24.8476 22.6835 24.8476 23.3165 24.4571 23.7071C24.1021 24.0621 23.5465 24.0944 23.1551 23.8039L23.0429 23.7071L16.5084 17.1727C14.8459 18.5236 12.7259 19.3333 10.4167 19.3333C5.07792 19.3333 0.75 15.0055 0.75 9.66667C0.75 4.32792 5.07792 0 10.4167 0ZM10.4167 2C6.18248 2 2.75 5.43248 2.75 9.66667C2.75 13.9008 6.18248 17.3333 10.4167 17.3333C14.6508 17.3333 18.0833 13.9008 18.0833 9.66667C18.0833 5.43248 14.6508 2 10.4167 2Z" fill="#767676"></path></svg>
        </button>
        <input
          ref="searchInput"
          v-model="query"
          placeholder='Try search "AI Stock" '
          @keyup.enter="fetchData"
        />
      </div>
    </div>
    <div class="quick-fill-buttons">
      <button @click="query='AI Stock'; fetchData()">AI Stock</button>
      <button @click="query='Youtube'; fetchData()">Youtube</button>
      <button @click="query='Dota2 online game'; fetchData()">Dota2</button>
      <button @click="query='Yoga Pants'; fetchData()">Yoga Pants</button>
      <button @click="query='Ergonomic Chair'; fetchData()">
        Ergonomic Chair
      </button>
      <button @click="query='PlayStation 5'; fetchData()">PS5</button>
      <button @click="query='EV car'; fetchData()">EV Car</button>
    </div>

    <div v-if="isLoading" class="skeleton">
      <div class="skeleton-body">
        <div class="skeleton-line"></div>
        <div class="skeleton-line"></div>
        <div class="skeleton-line"></div>
      </div>

      <div class="skeleton-body">
        <div class="skeleton-line"></div>
      </div>

      <div class="skeleton-body">
        <div class="skeleton-line"></div>
        <div class="skeleton-line"></div>
      </div>

      <div class="skeleton-body">
        <div class="skeleton-line"></div>
      </div>

      <div class="skeleton-body">
        <div class="skeleton-line"></div>
        <div class="skeleton-line"></div>
        <div class="skeleton-line"></div>
      </div>
    </div>

    <!-- NFT Card, visible when isLoading is false -->
    <template v-if="!isLoading && cards">
    <div class="cards-container">
      <div v-for="(card, cardIndex) in cards" :key="'card-popup-' + cardIndex" class="nft-card">
        <img width="50" height="50" :src="card.logo_url" alt="" />
        <h3>{{ card.company_name }} ({{ card.company_symbol }})</h3>
        <p>{{ card.opinion }}</p>
      </div>
    </div>
  </template>

    <div v-if="!isLoading && searchTime" class="disclaimer">
      <p class="search-time">Search Completed in {{ searchTime }} seconds</p>
      <p>
        Please note that the symbol may not up to date. Beacuse this model
        generally does not possess knowledge of events that have occurred after
        the vast majority of its training data was collected (i.e., before
        September 2021), therefore it may not include recent developments or
        changes in company strategy, market conditions, or other factors that
        could affect the relevance of these suggested matches.
      </p>
    </div>

  </div>
</template>

<script>
import axios from 'axios';
import mixpanel from 'mixpanel-browser';

mixpanel.init("f4a21b2032c39127a1cfeb503951376d", { debug: true, track_pageview: true, persistence: 'localStorage' });

// Track an event. It can be anything, but in this example, we're tracking a Sign Up event.
mixpanel.track('Open Page', {
  'PageView': 'SearchPage'
})

export default {
    data() {
    return {
        query: '',
        card: null,
        isLoading: false,
        searchTime: null,
        searchCost: null,
        companies: [],
        cards: [],
        cache: {}
    };
    },
    async created() {

    const response = await fetch('/company.json');
    const data = await response.json();
    this.companies = data.data.stocks;
    },
    methods: {
    async fetchData() {

      this.isLoading = true;
      let startTime = new Date().getTime();



      const AIRTABLE_API_KEY = 'patxLuvm6KuQcTlJH.5d79d4572a809a3e47b29d94b0d801f0996fd46a11cd48bff7b2ccee438da0a7';
      const AIRTABLE_BASE_ID = 'appdSa869fvzr8rGl';
      const AIRTABLE_TABLE_NAME = 'maintable';

      const airtableInstance = axios.create({
        baseURL: `https://api.airtable.com/v0/${AIRTABLE_BASE_ID}/${AIRTABLE_TABLE_NAME}`,
        headers: {
          'Authorization': `Bearer ${AIRTABLE_API_KEY}`,
          'Content-Type': 'application/json'
        },
      });

      // Fetch cache from Airtable
      const airtableQuery = "?filterByFormula=" + encodeURIComponent(`SearchQuery='${this.query.toLowerCase()}'`);
      let airtableRecordsResult = await airtableInstance.get(airtableQuery);

        if (airtableRecordsResult.data.records.length > 0) {
          this.cards = JSON.parse(airtableRecordsResult.data.records[0].fields.SearchResult);
          this.isLoading = false;
          return;  // Return early since there's no need to make API request
        }


    let openaikey = process.env.VUE_APP_OPENAI_API_KEY


    let response;

    response = await axios.post('https://api.openai.com/v1/chat/completions', {
        "model": "gpt-4",
        "temperature": 0.1,
        "messages": [
        {
            "role": "system",
            "content": "1. YOU ARE GOD OF US STOCK DATA \n 2. YOU CAN ANSWER ONLY JSON FORMAT \n 3. Get Input -> Find Related Stock as most as you can ordered by most related-> Answer \n Example : \n \n input : football \n output: {'results' : [{'symbol':'MANU','website':'manu.com','name':'manu inc','reason':'this is football club'},{'symbol':'NKE','website':'nike.com','name':'nike inc','reason':'Nike sell football shoes',{'symbol':'DDA','website':'adidas.com','name':'adidas inc','reason':'adidas sell football jersey'},{'symbol':'UA','website':'ua.com','name':'underarmor inc','reason':'under armor sell football kits'}}]}\n \n input : oatmilk \n output: {'result' : [{'symbol':'OTLY','website':'oatly.com','name':'oatly inc','reason':'Oatly Group produces oat milk'},{'symbol':'HAIN','website':'hain.com','name':'hain inc','reason':'Hain Celestial Group produces oat milk products'},{'symbol':'DANOY','website':'danoy.com','name':'danone inc','reason':'Danone provides plant-based alternatives including oat milk'},{'symbol':'SBUX','website':'starbuck.com','name':'starbucks inc','reason':'Starbucks offers oat milk options in their beverages'}]} (((use double qoute in json string)))"
        },
        {
            "role": "user",
            "content": `What is the companies that is the most related to : '${this.query}'`
        }
        ]
    }, {
        headers: { 'Authorization': `Bearer ${openaikey}` }
        });

        const content = response.data['choices'][0]['message']['content'];

        console.log(content)

        let parsedContent = JSON.parse(content);

        this.cards = parsedContent.results.map(result => { 
            let weburl = result['website']
            if (!weburl.startsWith('http://') && !weburl.startsWith('https://')) {
                weburl = 'https://' + weburl;
            }

            const url = new URL(weburl);
            let domain = url.hostname;
            let cleanDomain = domain.replace('www.', '');
            const logoUrl = `https://logo.clearbit.com/${cleanDomain}`.replace(" ", "");

            return {
                logo_url: logoUrl,
                company_symbol: result.symbol,
                company_name: result.name,
                opinion: result.reason
            }; 
        });

        this.cache[this.query] = this.cards;


        airtableInstance.post('/', {
          fields: {
            SearchQuery: this.query.toLowerCase(),
            SearchResult: JSON.stringify(this.cards)
          }
        });

        this.isLoading = false;

        let endTime = new Date().getTime();
        this.searchTime = ((endTime - startTime) / 1000).toFixed(2);

        const total_tokens = response.data['usage']['total_tokens'];

        const cost = (total_tokens / 1000) * 0.03;

        this.searchCost = cost

        this.$nextTick(() => {
            document.querySelector(".search-section").scrollIntoView({ behavior: 'smooth' });
        });

        mixpanel.track("Search Result", {
        "User Query": this.query,
        // use a comma-separated string of company symbols, names and opinions
        "Company Symbol": this.cards.map(card => card.company_symbol).join(", "),
        "Company Name": this.cards.map(card => card.company_name).join(", "),
        "Company Opinion": this.cards.map(card => card.opinion).join(", "),
        "Process Time": this.searchTime,
        "Search Cost": this.searchCost
      });

    }
},
      mounted() {
        this.$refs.searchInput.focus();
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
  margin-bottom: 0.5rem;

  @media (max-width: 768px) {
    flex-direction: column;
    align-items: center;
  }
}

#company-logo {
  width: 150px;
  height: auto;
  border-radius: 100px;
}

.logo-text {
  margin-top: -10px;
  text-align: center;

}

.logo-text h2 {
  font-size: 19px;
  font-weight: bold;
  @media (max-width: 655px) {
    font-size: 3vw;
  }
}

.logo-text p {
  font-size: 15px;
  color: #555;
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

  @media (max-width: 768px) {
    width: 95%;
  }
}

input::placeholder {
  font-size: 2.5vh;
  /* You can provide your desired font size */
}

.search-box input {
  border: none;
  flex: 1;
  display: flex;
  flex-grow: 1;
  margin-right: 10px;
  margin-left: 1rem;
  font-size: 25px;

  @media screen and (max-width: 768px) {
    font-size: 25px;
    min-width: 50%;

  }
}

.search-box input:focus {
  output: none;
  box-shadow: 0 0 0 30px rgba(0, 123, 255, 0);
  min-height: 44px;
  line-height: 42px;
  font-size: 25px;
  margin-left: 1rem;
  outline: none;
  box-shadow: 0 0 5px rgba(81, 203, 238, 0);
  @media screen and (max-width: 768px) {
    font-size: 25px;
    min-width: 25px;
  }
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
.search-section {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.quick-fill-buttons {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin-top: 5px;
}

.quick-fill-buttons button {
  background-color: #555555;
  color: white;
  border-radius: 10px;
  border: none;
  margin: 5px;
  padding: 5px 10px;
  cursor: pointer;
  min-width: auto;

  @media (max-width: 768px) {
    min-width: auto;
  }
}

.quick-fill-buttons button:hover {
  background-color: #9f4c3c;
}

.search-time {
  font-size: 14px; //update this
  margin-top: 1rem;
}

.disclaimer p{
  font-size: 10px; //update this
}

.cards-container {
    display: flex;
    overflow-x: auto;
    gap: 1rem;
    padding: 1rem 0;
    max-width: 98%;
  }

.nft-card {
  flex: 0 0 auto; /* Allow cards to shrink but not to grow, effectively disabling stretching in the flexbox layout */
  border: 0px solid #ddd;
  border-radius: 60px;
  padding: 1rem;
  margin: 1rem auto;
  text-align: center;
  max-width: 90%;
}

.search-time {
  text-align: center;
  font-size: 15px;
  color: #555;
  margin-top: 1rem;
}

.disclaimer {
  width: 100%;
  margin-top: 0rem;
  padding: 0.5rem;
  text-align: center;
  color: #555;
  font-size: 12px;
  background-color: #f0f0f0;
  border-radius: 10px;
  box-sizing: border-box;
  max-width: 584px;
  margin-bottom: 2rem
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
