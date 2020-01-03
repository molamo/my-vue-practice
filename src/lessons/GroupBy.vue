<template>
  <div>
    <h1>Group By</h1>
    <table class="table">
      <thead>
        <tr>
          <td>Rating</td>
          <td v-for="number in rowLength" :key="number">{{number}}</td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(stocks,rating, index) in groypByRating" :key="index">
          <th>{{rating}}</th>
          <td v-for="(symbol,index) in stocks" :key="index">{{symbol}}</td>
        </tr>
      </tbody>
    </table>

    <table class="table">
      <thead>
        <tr>
          <td>建議購買</td>
          <td v-for="number in rowRecoLength" :key="number">{{number}}</td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(stocks,recommendation, index) in groypByRecommendation" :key="index">
          <th>{{recommendation}}</th>
          <td v-for="(symbol,index) in stocks" :key="index">{{symbol}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>
import { mapActions } from "vuex";
export default {
  data: () => ({
    groypByRating: {},
    groypByRecommendation: {},

    rowLength: 0,
    rowRecoLength: 0
  }),
  async mounted() {
    const promise1 = this.getRating("AAPL");
    const promise2 = this.getRating("MSFT");
    const promise3 = this.getRating("FB");
    const promise4 = this.getRating("ZNGA");
    const promise5 = this.getRating("NVDA");
    const promise6 = this.getRating("WBA");
    const promise7 = this.getRating("AMZN");
    const promise8 = this.getRating("PIH");
    const allPromises = [
      promise1,
      promise2,
      promise3,
      promise4,
      promise5,
      promise6,
      promise7,
      promise8
    ];
    const stocks = await Promise.all(allPromises);

    console.log({
      stocks
    });

    //用Set找出key
    const ratingSet = new Set();
    const recommendationSet = new Set();
    stocks.forEach(stock => {
      const { rating } = stock;

      ratingSet.add(rating.rating);
      recommendationSet.add(rating.recommendation);
    });

    //第一種寫法
    // const A = stock.A
    // const B = stock.B
    // const C = stock.C
    //第二種寫法
    // const {A, B,C} = stock

    //console.log({ ratingSet });
    //console.log({ recommendationSet });
    //轉成Aarray
    const ratings = Array.from(ratingSet);
    const recommendationArray = Array.from(recommendationSet);
    //console.log({ recommendationArray });
    //用Array跑group by
    const groupByRating = {};
    const groupByRecommendations = {};

    ratings.forEach(rating => {
      const matchedStocks = stocks.filter(stock => {
        return stock.rating.rating === rating;
      });
      const stockSymbols = matchedStocks.map(stock => {
        return stock.symbol;
      });
      groupByRating[rating] = stockSymbols;
    });

    recommendationArray.forEach(rating => {
      //console.log(rating);
      const matchedStocks = stocks.filter(stock => {
        return stock.rating.recommendation === rating;
      });
      const stockSymbolsq = matchedStocks.map(stock => {
        return stock.symbol;
      });
      groupByRecommendations[rating] = stockSymbolsq;
    });

    this.groypByRecommendation = groupByRecommendations;
    console.log({ groupByRecommendations });
    this.groypByRating = groupByRating;

    // 底下調整table寬度兼炫技請無視
    const stocksByRating = Object.values(groupByRating);
    const rowLengths = stocksByRating.map(stocks => {
      return stocks.length;
    });
    const maxLength = rowLengths.reduce(function(a, b) {
      return Math.max(a, b);
    });
    this.rowLength = maxLength;
    // 複製樓上炫技的程式碼偷改的
    const RecommendationsByRating = Object.values(groupByRecommendations);
    const rowRecoLengthtmp = RecommendationsByRating.map(stocks => {
      return stocks.length;
    });
    //console.log(rowRecoLengthtmp);
    const maxLengthtmp = rowRecoLengthtmp.reduce(function(a, b) {
      return Math.max(a, b);
    });
    //console.log("maxLengthtmp:" + maxLength);
    this.rowRecoLength = maxLengthtmp;
  },
  methods: {
    ...mapActions(["getRating"])
  }
};
</script>