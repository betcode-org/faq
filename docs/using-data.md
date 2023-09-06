## Betfair Data

??? question "I’m starting to use the API to collect data. Do I need to place bets?"
    The short answer is yes. But how many? We don't know.

    The key principle is that Betfair allows you access to their data for your personal betting activities, but they sell it to other users. So they want to be sure that you're not collecting it for commercial use. Show them some betting activity as evidence that you're using it only for your personal use and you'll be OK.

??? question "How do I filter streams by competition?"
    Sadly, you can't. When using Flumine the best approach is to include a condition in your strategy's <code>check_market_book</code> method to discard the competitions that you're not interested in.

    Betfair are aware of this limitation, but apparently it's a lot of work to remove.

??? question "How do I match runner names to runner IDs?"
    Betfair market books allow you to see the prices ladder for each runner ID (selection_id) but not the name. The names for each runner can be found in the market catalogue.

    When running a live strategy, Flumine will automatically inject the market catalogue into your data in `market.market_catalogue`, but when backtesting it's only available if you recorded it at the time. If you did, then you can inject it into your testing results using [the market catalogue middleware](https://github.com/betcode-org/flumine/blob/master/examples/middleware/marketcatalogue.py).

??? question "How can I get the last traded price before a match starts without recording the whole market?"
    A rough and ready way would be to use Betfairlightweight to place an API call at the scheduled market time and use the last traded price at that point.
    However, if you're streaming market data in Flumine, there's very little overhead to storing last traded prices in the strategy's "context" dictionary `strategy.context` and overwriting them each time they market update changes them until the market goes "inplay". This would be the recommended approach in most situations.

??? question "The market start times don’t seem to match when events actually start in my country. Why is this?"
    Betfair datetimes are always for UTC timezone. You will need to translate them to your local timezone.

??? question "How do I get the trap number for greyhounds in Betfair?"
    The trap number is always the first character (and very, very rarely, first two characters) of the runner name. So if ```Droopys Child``` is running in trap 3, the selection name in the market catalogue will be ```3. Droopys Child```. If that dog is a non-runner, then for UK greyhounds it might be replaced by ```3. Flopsy Bunny (res)```. for Australian greyhounds the replacement might be ```9. Flopsy Bunny```, with sort priority 3.

??? question "I see that I can download or buy historical data from Betfair. Is it worth it?"
    Betfair historical data comes in three flavours: basic, advanced and pro. The [specification for each package is available here](https://historicdata.betfair.com/Betfair-Historical-Data-Feed-Specification.pdf). Basic data is free but generally of little value as it contains the last traded price at minute intervals and little else. Advanced offers data on best prices and volumes, but with a 1 second frequency only. Pro data is intended to provide the same content and granularity as if you were streaming it yourself.

    The channel's most common recommendation is to stream and collect it yourself. If you have time constraints or deep pockets, the most common recommendation is not use only the Pro version unless and exceptionally your intended strategy will be specifically geared to the lower frequency of the Advanced data.

    If you are considering purchasing data, do checkout what [free advanced and pro data](https://historicdata.betfair.com/#/home) Betfair might have available at the time.

??? question "Where can I get historical Betfair starting prices?"
    Historic BSPs are only available for horse and greyhound racing and can be found [on the Betfair prices page](https://promo.betfair.com/betfairsp/prices), though the data is sometimes incomplete.

## Third Party Data

??? question "Where can I get historic starting prices for football matches?"
    Until recently, Betfair didn't have starting prices for football (soccer) matches. It is currently rolling them out to a limited number of top level matches, but the starting prices still aren't published after the event, and may never be. Alternatives are bookmaker starting prices and Betfair pre-match closing prices.
    
    Bookmaker starting prices (aka market closing prices) for major leagues are available for free from [football-data.co.uk](https://football-data.co.uk/) and a host of paid-for services.

    Betfair pre-match closing prices can be recorded by streaming markets, saving the current prices to the market.context dcitionnary and overwriting the values each time there's an update, until the market goes in-play.

??? question "I’ve been using a third-party soccer feed, but the team names don’t always match with Betfair's. How do other people handle this?"
    There's no easy answer to this. Many people build and maintain mappings between the data provided by different providers.

    These mappings may be created manually or programmatically by using a combination of event matching and fuzzy text matching. So for example, by matching all soccer teams in the same league, or all the horses in the same race, you will usually be left with only a very small number that don't match easily.
    Although not a direct answer to your question, you may also find these [Betfair mappings](https://github.com/mberk/betfairmappings) helpful.
