# Deploying Strategies

## New Strategies
??? question "I’m about to run a new strategy live. What should I do to protect myself in case it goes horribly wrong?"
    Before running a new strategy live, it is recommended that you paper trade for a short period. Assuming you have backtested properly, it should work with similar results. However, it's a good way to uncover any environmental differences.

    When you're ready to trade with real money, you may want to protect yourself by limiting the amount of funds in your account. One way to do this without actually withdrawing funds from Betfair, is to temporarily transfer any surplus from the account's "Main Wallet" to the "Exchange Games Wallet" where it can be parked until you are confident that the strategy won't run rampant.

??? question "Is there a way to force my strategy to process market books periodically, even when there’s no update within that period?"
    There is. You can set the streaming_timeout parameter for your strategy and it will force an update after the specified number of seconds.

??? question "What is conflate_ms used for when creating a strategy?"
    By default Betfair’s matching engine runs every 50 milliseconds and streams updates to subscribed channels. However, if you don’t want to receive them that frequently, you can set this parameter to a higher value. You might wish to do this if, for example, your strategy isn’t particularly time-sensitive, but each update requires substantial processing.

??? question "I am about to put my new strategy live - how will I add new markets to the stream when they're opened?"
    You can't. Instead, when defining your market filter, don't list specific markets, just describe the sort of markets you want to stream. Event types, market types and countries are good for this. Then when Betfair adds new markets to the exchange it will automatically add them to your stream if they satisfy your filters.

## Resources
??? question "Should I run a separate instance of Flumine for each of my strategies?"
    Usually no. Flumine is capable of automatically merging the streams needs for multiple strategies into a single subscription and then delivering the market updates needed for each strategy to those strategies alone. This makes the streaming more efficient.

    Exceptions to this would be where the strategies are in different sports, or would result in subscribing to more markets that your Betfair limit per subscription.

??? question "Has anyone tried streaming using AWS or any other serverless? And any recommendations on the same?"
    Don't do it. Serverless is priced per invocation of the code and you'll have a huge number of them. It's straightforward and much more cost effective to spin up a dedicated EC2 instance and run your code on there

## Problems
??? question "I’m getting lots of log messages about latency. What do they mean and what should I do?"
    When a Flumine strategy receives a market book update, it compares the updates timestamp to the clock where you are processing it. If there's a significant difference, usually a delay, it logs a warning message,
    
    There are several possible causes:
    
    * Your clock may be out of sync with real-time. We recommend that you ensure that it is regularly synchronised to an atomic clock to avoid this issue.
    * There may be a network delay such as a traffic spike at your ISP, or a family member streaming a Netflix movie, causing a slowing down the rate at which updates are getting to you - these are usually temporary and typically clear themselves.
    * Sometimes it will be because your processing of each update is too complex or inefficient and isn't completing before the next one arrives. This is likely to be the case if the latency duration is climbing. In which case you will need to [profile](https://docs.python.org/3/library/profile.html) your code to identify and deal with the processing bottleneck.
