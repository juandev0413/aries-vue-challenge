<!-- 

## Objective

Design and implement a front-end for options strategy risk and reward analysis using Vue.

## Brief

Your challenge is to create a Vue component that can generate a risk & reward graph for options strategies. The component should accept an input of up to four options contracts and output the following:
1. A risk & reward graph where X is the price of the underlying at the time of expiry and Y is the profit/loss at that price. 
2. Max profit, max loss, and all break even points.

### Evaluation Criteria

- Completeness of the logic
- Usability of the graph
- Aesthetics of the UI
- Readability and code structure

### CodeSubmit 

Please organize, design, test, and document your code as if it were
going into production - then push your changes to the main branch.

Reply to the invitation e-mail with your github username to notify of completion.

Have fun coding! 🚀
The Aries Financial Team -->


Explanation:
	Data Initialization:
	    options: 
            An array of objects representing each options contract with properties for strike price (strike_price), type (type), bid (bid), ask (ask), long/short (long_short), and expiration date (expiration_date).
    Template:
        Input section dynamically generates input fields based on the options array.
        Each option includes input fields for strike price, buy/sell (long/short), bid, and ask prices.
    Methods:
        generateChart(): Computes profit/loss for each underlying price based on the bid/ask prices and generates the Chart.js line chart.
        calculateProfitLoss(): Calculates profit/loss for each underlying price considering bid/ask prices and option type (long/short).
        calculateMaxProfitLoss(): Determines the maximum profit, maximum loss, and break-even points based on the computed profit/loss data.
    Chart Configuration:
        Uses Chart.js to render a line chart (type: 'line') that visualizes the profit/loss at expiration based on the underlying price.
        Customizes aesthetics with specified colors, hides points for a cleaner look, and formats tooltips for better readability.
        Responsive design considerations are included with media queries to adjust chart section width for smaller screens.
    Usage:
        1.	Setup: Ensure Vue.js (npm install vue) and Chart.js (npm install chart.js) are installed in your project.
        2.	Component Files: Place the updated CodingChallenge.vue in your components/ directory.
        3.	Run: Start your Vue application (npm run serve if using Vue CLI).
        4.	Interaction: Input details for each options contract (strike price, type, bid, ask), click "Generate Chart" to see the risk & reward graph, and view the max profit, max loss, and break-even points.
