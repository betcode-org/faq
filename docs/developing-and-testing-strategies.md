# Developing and Testing Strategies

## Finding Strategies

??? question "What would be a profitable strategy for horse racing?"
    We will answer almost anything else about how to automate your betting, find strategies, and assess their profit potential. But we won't give you profitable strategies.

    There's a good reason for this. Many of the channel's members make their living from betting in sports markets. But those markets are very dynamic and are constantly evolving. So when a strategy becomes public, markets evolve to remove the inefficiency that made it profitable. So the strategy that you're given would very probably no longer be profitable by the time you've implemented it and the person who gave it to you would have lost some of their income.

??? question "Can you recommend a good book to help me deepen my understading of betting markets"
    There are an awful lot of truly terrible books out there. Generally if they include "win money daily", "bet profitabily" or similar in the title - stay well away.

    However there are also some very useful and interesting books. Though some are quite expensive, the following have been recommended by active bettors on our Slack channel:

    * [The Logic of Sports Betting](https://www.amazon.co.uk/Logic-Sports-Betting-Ed-Miller/dp/1096805723)
    * [Trading Bases](https://www.amazon.co.uk/Trading-Bases-Fortune-Betting-Baseball/dp/0451415175)
    * [Statistical Sports Models in Excel](https://www.amazon.co.uk/Statistical-Sports-Models-Excel-Andrew/dp/1079013458)
    * [Precision: Statistical and Mathematical Methods in Horse Racing](https://www.amazon.co.uk/Precision-Statistical-Mathematical-Methods-Racing/dp/1432768522)
    * [Efficiency of Racetrack Betting Markets](https://www.amazon.co.uk/Efficiency-Racetrack-Scientific-Financial-Economics/dp/981320351X)
    * [The Kelly Capital Growth Investment Criterion: Theory and Practice](https://www.amazon.co.uk/Kelly-Capital-Growth-Investment-Criterion/dp/9814383139)
    * [A Man for All Markets: Beating the Odds, from Las Vegas to Wall Street](https://www.amazon.co.uk/Man-All-Markets-Beating-Street/dp/1786071975)
    * [Soccernomics](https://www.amazon.co.uk/Soccernomics-France-Germany-England-Starting/dp/0008559627)
    * [The Signal and the Noise: The Art and Science of Prediction](https://www.amazon.co.uk/Signal-Noise-Art-Science-Prediction/dp/0141975652)

??? question "What’s the difference between technical indicators and fundamentals and what do people here use?"
    Technical indicators are extracted from what's happening in a betting market, such as prices, trading volumes matched per runner, amounts available to back or lay, when liquidity arrives in the market, etc. fundamentals ignore what's happening in the market and focus on real world data - how well has that horse been running recently? what's that teams form like? which dog runs best when it's raining? and so on ...

    This channel has members with clear preferences for one or the other and many will use both, either for different strategies or by "stacking" them in the same strategy.

    In general, it's easier to work with technical indicators, but if you have a good understanding of the key factors for the sport in which you'll be betting, fundamentals may offer better opportunities.

??? question "Can AI/ML help with developing betting strategies?"
    Opinions here are mixed. There are members who run successful strategies with just a few "if" statements. On the other hand we have huge amounts of data available to us and for some that's a natural opportunity to use ML approaches leading to AI models.

    If the latter appeals to you, go for it, but with care. Sports data is very noisy and it's incredibly easy to mine opportunities that aren't real, so ensure that you know how to avoid overfitting your models and properly evaluating their accuracy.

??? question "Where can I find a list of all possible Betfair market types for my favourite sport?"
    Sadly there's no foolproof way to do this. Different events of the same type will typically have a core set of markets, but popular events may have others added if Betfair believes that there will be sufficient interest amongst bettors. Some of these additional markets may be quite specific and appear infrequently.

    To get an idea of the specific market types available currently for, say, Ice Hockey, you could run a small script like this:

    ``` python
    results = trading.betting.list_market_catalogue(
        filter=betfairlightweight.filters.market_filter(
            event_type_ids=["7524"],  # ice hockey
        ),
        market_projection=["MARKET_DESCRIPTION"],
        max_results=200,
    )
    print({result.description.market_type for result in results})
    ```

## Implementing Strategies

??? question "I’ve been trading manually for a while and want to automate my strategies. How do I start?"
    You will want to take advantage of the Flumine trading framework. For an easy to understand introduction to how strategies are structured in Flumine, take a look at the [lowest layer strategy](https://github.com/betcode-org/flumine/blob/master/examples/strategies/lowestlayer.py) in the flumine examples folder.

??? question "When should I use middleware or a worker?"
    Middleware is run each time there's a market update immediately before that update is delivered to your strategy, so it is extra processing that will add to your strategy's execution time. Workers run on a different thread at a frequency that you set when you initiate them, so should have minimal, if any, impact on your strategy's execution.

    So if you want to change or extend the market information, for example by calculating a technical indicator from the updated market data, then you would probably want to use middelware. However, if you were periodically making calls to an external API, to obtain match statistics for example, you would most likely want to do this in parallel to your strategy execution and so would use a worker.

    One exception would be when backtesting. The simulator works by patching the system clock to the time the event was taking place, as it's rather difficult to keep patched times in sync on different processors, workers don't play nicely with backtesting and you will need to use middleware to achieve accurate results.

??? question "How does Flumine handle abandoned events?"
    This isn't something that Flumine handles explicitly. Rather it deals with the updates received from Betfair in exactly the same way as it would when the same updates information are received in a normal race. So the question shifts to what updates does Betfair provide for an abandoned race?
    The answer is that it first suspends the affected markets, and sets all the available ladder volumes to zero, Then it removes all the runners and closes the markets. There's no explicit "abandoned" status or reason given for closing each market.

    It will also cancel all the orders on the affected markets, which Flumine will detect in the order stream if you have that running (i.e. haven't overriden the default).

??? question "Why does the get_runner_context function require the runner handicap when it's always zero?"
    The get_runner_context function needs three parameters to ensure that it returns a unique context: market_id, selection_id and handicap. The handicap is not the same as the weight handicap in a horse race or distance handicap in a greyhound race. rather it is the articial handicap added to asian handicap markets (e.g. Man United to win by more than one goal). So although it is zero for most market types, for asian handicaps we need to combibe the selection_id and handicap to identify a unique outcome.

    Simialarly there are other market types for which we can't rely on selection_id and (zero) handicap to uniquely identify the outcome. For example, draws in all soccer have the same selection_id, so we need to add the market_id to uniquely identify the specific outcome for which we want the runner context.

??? question "How do I merge information from the Betfair market catalogue with the market data"
    If you're using Flumine this will happen automatically if you're using a live stream i.e. for live or paper trading. But it doesn't happen immediately, rather it ca tale a minute or so before it appears. When it is available you will be able to access the catalogue object at market.market_catalogue.

    If you're backtesting then the data will be available only if you collected it at the time of the event, which is usually done using the [marketrecorder](https://github.com/betcode-org/flumine/blob/master/examples/marketrecorder.py). You'll then need to tell Flumine where to find the catalogue file. This is done by creating a middleware file based on the [example in the Flumine repo](https://github.com/betcode-org/flumine/blob/master/examples/middleware/marketcatalogue.py), and then enabling it with:

    ```python
    framework.add_market_middleware(MarketCatalogueMiddleware())
    ```

??? question "In the blotter when I get ```live_orders```, is this across all strategies? And what is considered a live order?"
    They are orders which have not been completed, voided or expiredin the market for all strategies. Most of the time this will mean that they are 'executable' (have ```OrderStatus.EXECUTABLE```), but they could be, briefly, ```PENDING```, ```CANCELLING```, ```REPLACING``` or ```UPDATING``` as well.
    
    This differs from the orders that are passed to the your strategy's ```process_orders``` method, which will receive all orders, irrespective of the status, but only for that specific strategy.

??? question "I'd like my Strategy to perform some action when it stops (either because I stop the script manually with Ctrl+C but also in case of a bug). Where should I put this code?"
    You should overwrite the [finish](https://github.com/betcode-org/flumine/blob/master/flumine/strategy/strategy.py#L154C13-L154C13) method, this method is called before flumine ends.

??? question "How can I keep the state of my flumine Strategy after a restart?"
    Take a look at the Middleware [orders.py](https://github.com/betcode-org/flumine/blob/master/examples/middleware/orders.py). It will add execution complete orders to the blotter, so the exposures you had will be correctly loaded after a restart of the same Strategy.

## Testing Strategies

??? question "How do I know if my strategy will be successful?"
    Ah. if only there was an easy answer to this question. But there are some steps that you can take to be confident that it will be, or to avoid losing money if it won't.

    Every new strategy should be extensively backtested. This means running it against historical data. Flumine can make this easy for you as it's usually possible to code a strategy as it will be if you take it live and instead pass it a list of historical markets for which you have data to run it against those instead and then evaluate the results.

    To evaluate the results you would look at two things. Did it generate profits and did you have an adequate sample size to have confidence in those profits. Successful sports bettors may work with profits as low as 1-3% return on investment after commission has been deducted. There will be winning streaks and losing streaks, but provided you have a large enough sample these will even out over time. A typical sample size should certainly cover multiple months and for sports such as racing and soccer, thousands of markets.

??? question "How do I compare each-way prices with win and place markets when backtesting in Flumine?"
    When backtesting strategies, Flumine by default streams each market in turn delivering the price updates for that market before moving on to the next one. This is different to what would happen when running live when updates are received for multiple markets at the same time.

    The real-world situation can be replicated however by activating event processing. This will merge the updates for a given event and deliver them in the order that they originally occurred, and can be enabled as follows:

    ``` python
    strategy = ExampleStrategy(
        market_filter={"markets": [..], "event_processing": True}
    )
    ```

    Each market in an event is then made available to the others e.g.

    ```win_market = market.event["WIN"][0]``` (a list of market objects of the requested type is returned)

    This will also work for other sports when an event has multiple markets, however, sadly, it won't work for greyhound races.
