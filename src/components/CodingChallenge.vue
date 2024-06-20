<template>
  <div class="container">
    <div class="input-section">
      <h1>Options Profit Calculator</h1>
      <div v-for="(option, index) in options" :key="index" class="option-input">
        <div class="option-fields">
          <label class="option-label">Option {{ index + 1 }}:</label>
          <div class="field-group">
          </div>
          <div class="field-group">
            <label class="field-label">Strike Price:</label>
            <input type="number" v-model="option.strike_price" class="field-input" />
          </div>
          <div class="field-group">
            <label class="field-label">Buy/Sell:</label>
            <select v-model="option.long_short" class="field-input">
              <option value="long">Long</option>
              <option value="short">Short</option>
            </select>
          </div>
          <div class="field-group">
            <label class="field-label">Bid:</label>
            <input type="number" step="0.01" v-model="option.bid" class="field-input" />
          </div>
          <div class="field-group">
            <label class="field-label">Ask:</label>
            <input type="number" step="0.01" v-model="option.ask" class="field-input" />
          </div>
        </div>
      </div>
      <button @click="generateChart" class="generate-button">Generate Chart</button>
    </div>
    <div class="chart-section">
      <h2>Risk & Reward Graph</h2>
      <div class="chart-container">
        <canvas ref="myChart"></canvas>
      </div>
      <div class="stats">
        <p><strong>Max Profit:</strong> {{ maxProfit.toFixed(2) }}</p>
        <p><strong>Max Loss:</strong> {{ maxLoss.toFixed(2) }}</p>
        <p><strong>Break-Even Points:</strong> {{ breakEvenPoints.join(', ') }}</p>
      </div>
    </div>
  </div>
</template>

<script>
  import Chart from 'chart.js/auto';

  export default {
    props: {
      optionsData: {
        type: Array,
        required: true
      }
    },
    data() {
      return {
        options: [],
        underlyingPrices: Array.from({ length: 101 }, (_, i) => 80 + i), // Generate underlying prices from 80 to 180
        myChart: null,
        maxProfit: 0,
        maxLoss: 0,
        breakEvenPoints: []
      };
    },
    created() {
      // Initialize options with provided data
      this.options = this.optionsData.map(option => ({
        strike_price: option.strike_price,
        type: option.type,
        bid: option.bid,
        ask: option.ask,
        long_short: option.long_short,
        expiration_date: option.expiration_date
      }));
    },
    methods: {
      generateChart() {
        // Calculate profit/loss for each underlying price
        const profitLossData = this.calculateProfitLoss();

        // Ensure the chart is destroyed before re-rendering
        if (this.myChart) {
          this.myChart.destroy();
        }

        // Render the chart
        this.myChart = new Chart(this.$refs.myChart.getContext('2d'), {
          type: 'line',
          data: {
            labels: this.underlyingPrices.map(price => price.toString()),
            datasets: [{
              label: 'Profit/Loss',
              data: profitLossData,
              borderColor: 'rgb(75, 192, 192)',
              backgroundColor: 'rgba(75, 192, 192, 0.2)',
              borderWidth: 2,
              pointRadius: 0, // Hide points to keep the chart clean
              fill: true
            }]
          },
          options: {
            scales: {
              x: {
                title: {
                  display: true,
                  text: 'Underlying Price at Expiry'
                }
              },
              y: {
                title: {
                  display: true,
                  text: 'Profit/Loss'
                }
              }
            },
            plugins: {
              legend: {
                display: false // Hide legend for cleaner look
              },
              tooltip: {
                callbacks: {
                  label: function(tooltipItem) {
                    return tooltipItem.raw.toFixed(2); // Format tooltip to show profit/loss as decimal
                  }
                }
              }
            }
          }
        });

        // Calculate and update max profit, max loss, and break-even points
        this.calculateMaxProfitLoss(profitLossData);
      },
      calculateProfitLoss() {
        const profitLossData = [];

        // Iterate over each underlying price
        for (let price of this.underlyingPrices) {
          let totalProfitLoss = 0;

          // Calculate profit/loss for each option contract
          for (let option of this.options) {
            let profitLoss = 0;
            const strikePrice = option.strike_price;
            const quantity = 1; // Assuming quantity is 1 for each option

            if (option.long_short === 'long') {
              // Long call/put
              profitLoss += Math.max(price - strikePrice - option.ask, 0) * quantity;
            } else if (option.long_short === 'short') {
              // Short call/put
              profitLoss += Math.min(strikePrice - price - option.bid, 0) * quantity;
            }

            totalProfitLoss += profitLoss;
          }

          profitLossData.push(totalProfitLoss);
        }

        return profitLossData;
      },
      calculateMaxProfitLoss(profitLossData) {
        // Max Profit is the maximum value in profitLossData
        this.maxProfit = Math.max(...profitLossData);

        // Max Loss is the minimum value in profitLossData
        this.maxLoss = Math.min(...profitLossData);

        // Calculate Break-Even Points (where profit/loss is 0)
        this.breakEvenPoints = this.underlyingPrices.filter((price, index) => {
          return profitLossData[index] === 0;
        });
      }
    }
  };
</script>

<style scoped>
.field-input{
  width: 150px;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
  outline: none;
  padding-inline: 0px !important;
  padding-left:2px;
}


.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.input-section {
  background-color: #f0f0f0;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 20px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.option-input {
  margin-bottom: 10px;
}

.option-fields {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}

.option-label {
  font-weight: bold;
}

.field-group {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.field-label {
  width: 120px;
}



.generate-button {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin-top: 10px;
  cursor: pointer;
  border-radius: 4px;
  transition: background-color 0.3s ease;
}

.generate-button:hover {
  background-color: #45a049;
}

.chart-section {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.chart-container {
  position: relative;
  height: 400px; /* Adjust height as needed */
}

.stats {
  margin-top: 20px;
  text-align: center;
}

@media (max-width: 768px) {
  .option-fields {
    grid-template-columns: 1fr;
  }
  .field-label {
    width: 100%;
  }
  .field-input {
    width: 100%;
  }
}
</style>
