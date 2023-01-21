# Getting Started

## Tools

??? question "What are the differences between Betfairlightweight, Flumine and BetConnect?"
    Betcode-org currently supports three python packages: Betfairlightweight, BetConnect and Flumine. The Betfairlightweight and BetConnect packages are wrappers around the Betfair and Beconnect Betting Exchange APIs, allowing requests to be made to those services and responses to be handled.

    Flumine is a trading framework that sits on top of Betfairlightweight and BetConnect package. It handles a lot of the repetitive work needed to run strategies and so offers a quick, efficient and battle-tested structure for developing and executing betting strategies at those exchanges.

??? question "How do Flumine Strategies work?"
    There's a [technical overview](https://betcode-org.github.io/flumine/architecture/) of the structure in the documentation, but if you are looking for a more accessible entry point, take a look at the [lowest layer example](https://github.com/betcode-org/flumine/blob/master/examples/strategies/lowestlayer.py) in the code's examples folder.

    That example shows how, with only a few lines of code, you can filter the sports markets in which you want to trade, calculate conditions and place orders when they're met and then monitor orders and remove the ones that aren't matched.

## Technical Terms

??? question "What is "Streaming"?"
    If you want to know the current prices for an event at Betfair, you could post an API request for that information and receive back a response with that information. However, it would be out-of-date almost as soon as you receive it. So you could end up making huge numbers of requests just to stay current.

    This "polling" approach creates processing load for you and for Betfair's servers, so Betfair offers a much more efficient alternative, called streaming. When streaming, you first tell Betfair what types of markets you wish to follow (known as subscribing), they will send you the current state of each of those markets and then whenever there is a price change they'll automatically send ("push") an update to your data stream.

    As Betfair only normally sends changes, there is a lot less data needing to be sent. better still, as new markets are opened matching your original subscription, Betfair will automatically start to include these too in the data that it sends you.

    Betfairlightweight can manage subscriptions and updates to your stream and either pass them to you for processing directly, but it's easier to subscribe to streams and handle the responses in Flumine.

    It's also possible to stream orders, and if you use Flumine for your trading strategies, it will do this automatically for you.
    Streaming is not currently available for the BetConnect Exchange.

??? question "What are backtesting, paper trading and simulations?"
    Backtesting is a key step in betting strategy development. It means taking a strategy that you think may be profitable and seeing what the results would be if you were to run it against historical data.

    Paper testing is another way of testing a strategy whereby instead of placing real bets, you record the bets your strategy would have made live and then see what what the results would have been once the event has finished. Historically you would have written the details of the bets you would have placed, hence the name.

    As in both cases no bets are actually placed, we have to work out what the results would have been. Flumine can do this for us by simulating the market and assessing whether the bet would have been matched and then evaluating the resulting profit (or loss) when the market closed. So backtesting and paper trading through Flumine are both examples of simulations.

    It is important to note that the Flumine simulation engine has some real-world limitations. It can't anticipate the effect that placing bets will have on the market itself. So for example, placing a very large bet in real-life will often change how other bettors act, e.g. change prices, in ways that the simulator can't anticipate. 

## Services

??? question "What cloud services do people here use for running bots and storing data?"
    Although some use Google Cloud, the majority of active users of this channel seem to prefer AWS. So S3 is the preferred choice for storing historic data and EC2 instances for running bots however all packages are cloud agnostic and can run locally just as well.

    In addition, some use AWS Lambda for data ingestion and cleansing, and a variety of databases are used for storing meta data or real-time data.

??? question "I have a question that's not addressed here. From where can I get further help?"
    If you haven't already, it's certainly worth diving into the more [advanced topics in the documentation](https://betcode-org.github.io/flumine/clients/).

    If you still have questions, then the betcode community hangs out on [Slack](https://join.slack.com/t/betcode-org/shared_invite/zt-h0ato238-PPbfU_T7Ji0ORjz0ESIJkg).
