# Developing and Testing Strategies

## Developing Strategies

??? question "What would be a profitable strategy for horse racing?"
    We will answer almost anything else about how to automate your betting, find strategies, and assess their profit potential. But we won't give you profitable strategies.

    There's a good reason for this. Many of the channel's members make their living from betting in sports markets. But those markets are very dynamic and are constantly evolving. So when a strategy becomes public, markets evolve to remove the inefficiency that made it profitable. So the strategy that you're given would very probably no longer be profitable by the time you've implemented it and the person who gave it to you would have lost some of their income.

??? question "I’ve been trading manually for a while and want to automate my strategies. How do I start?"
    You will want to take advantage of the Flumine trading framework. For an easy to understand introduction to how strategies are structured in Flumine, take a look at the [lowest layer strategy](https://github.com/betcode-org/flumine/blob/master/examples/strategies/lowestlayer.py) in the flumine examples folder.

??? question "What’s the difference between technical indicators and fundamentals and what do people here use?"
    Technical indicators are extracted from what's happening in a betting market, such as prices, trading volumes matched per runner, amounts available to back or lay, when liquidity arrives in the market, etc. fundamentals ignore what's happening in the market and focus on real world data - how well has that horse been running recently? what's that teams form like? which dog runs best when it's raining? and so on ...

    This channel has members with clear preferences for one or the other and many will use both, either for different strategies or by "stacking" them in the same strategy.

    In general, it's easier to work with technical indicators, but if you have a good understanding of the key factors for the sport in which you'll be betting, fundamentals may offer better opportunities.

??? question "Can AI/ML help with developing betting strategies?"
    Opinions here are mixed. There are members who run successful strategies with just a few "if" statements. On the other hand we have huge amounts of data available to us and for some that's a natural opportunity to use ML approaches leading to AI models.

    If the latter appeals to you, go for it, but with care. Sports data is very noisy and it's incredibly easy to mine opportunities that aren't real, so ensure that you know how to avoid overfitting your models and properly evaluating their accuracy.

??? question "When should I use middleware or a worker?"
    Middleware is run each time there's a market update immediately before that update is delivered to your strategy, so it is extra processing that will add to your strategy's execution time. Workers run on a different thread at a frequency that you set when you initiate them, so should have minimal, if any, impact on your strategy's execution.

    So if you want to change or extend the market information, for example by calculating a technical indicator from the updated market data, then you would probably want to use middelware. However, if you were periodically making calls to an external API, to obtain match statistics for example, you would most likely want to do this in parallel to your strategy execution and so would use a worker.

    One exception would be when backtesting. The simulator works by patching the system clock to the time the event was taking place, as it's rather difficult to keep patched times in sync on different processors, workers don't play nicely with backtesting and you will need to use middleware to achieve accurate results.

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
