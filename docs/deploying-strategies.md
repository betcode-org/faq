# New Strategies

<details markdown="1">
<summary>I’m trying a new strategy. What should I do to protect myself in case it goes horribly wrong?</summary>
Before runnig a new strategy live, it is recommended that you paper trade for a short period. Assuming you've backtested properly, it should work with similar results. However, it's a good way to uncover any environmental differences.

When you're ready to trade with real money, you may want to protect yourself by limiting the amount of funds in your account. One way to do this without actually withdrawing funds from Betfair, is to temporarily transfer any surplus from the account's "Main Wallet" to the "Exchange Games Wallet" where it can be parked until you are cofident that the strategy won't run rampant.
</details>

<details markdown="1">
<summary>Is there a way to force my strategy to process market books periodically, even when there’s no update within that period?</summary>
There is. You can set the streaming_timeout parameter for your strategy and it will force an update after the specified number of seconds.
</details>

<details markdown="1">
<summary>What is conflate_ms used for when creating a strategy?</summary>
By default Betfair’s matching engine runs every 50 milliseconds and streams updates to subscribed channels. However, if you don’t want to receive them that frequently, you can set this parameter to a higher value. You might wish to do this if, for example, your strategy isn’t particularly time-sensitive, but each update requires substantial processing.
</details>

# Resources

<details markdown="1">
<summary>Should I run a separate instance of Flumine for each of my strategies?</summary>
Usually no. Flumine is capable of automatically merging the streams needs for multiple strategies into a single subscription and then delivering the market updates needed for each strategy to those strategies alone. This makes the streaming more efficient.

Exceptions to this would be where the strategies are in different sports, or would result in subscribing to more markets that your Betfair limit per subscription.
</details>

<details markdown="1">
<summary>Has anyone tried streaming using AWS or any other serverless? And any recommendations on the same?</summary>
Don't do it. Serverless is priced per invokation of the code and you'll have a huge number of them. It's straightforward and much more cost effective to spin up a dedicated EC2 instance and run your code on there
</details>

# Problems

<details markdown="1">
<summary>I’m getting lots of log messages about latency. What do they mean and what should I do?</summary>
This means that there is a significant delay between the timestamp that Betfair is putting on your market updates and the point when you are processing them. Often this will be because your processing is too slow and isn't completing before the next update comes in. This is likely to be the case if the latency duration is climbing. In the case you will need to profile your code to identify and deal with the processing bottleneck.
</details>
