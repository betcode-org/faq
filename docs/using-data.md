## Betfair Data

<details markdown="1">
<summary>I’m starting to use the API to collect data. Do I need to place bets?</summary>
The short answer is yes. But how many? We don't know.

The key principle is that Betfair allows you access to their data for your personal betting activities, but they sell it to other users. So they wants to be sure that you're not collecting it for non-personal use. Show them some betting activity as evidence that you're using it for personal use and you'll be OK.
</details>

<details markdown="1">
<summary>How do I filter streams by competition?</summary>
Sadly, you can't. When using Flumine the best approach is to include a condition in your strategy's <code>check_market_book</code> method to discard the competitions that you're not interested in.

Betfair are aware of this limitation, but apparently it's a lot of work to remove.
</details>

<details markdown="1">
<summary>How do I match runner names to runner IDs?</summary>
Betfair market books allow you to see the prices ladder for each runner ID (selection_id) but not the name. The names for each runner can be found in the market catalogue.

When runnning a live strategy, Flumine will automatically inject the market catalogue into your data, but when backtesting it's only available if you recorded it at the time. If so, you can inject it into your testing results using <a href="https://github.com/betcode-org/flumine/blob/master/examples/middleware/marketcatalogue.py">this middleware</a>.
</details>

<details markdown="1">
<summary>How can I get the last traded price before a match starts without recording the whole market?</summary>
A rough and ready way would be to use Betfairlightweight to place an API call at the scheduled market time and us the last traded price at that point.
However, if you're streaming data in Flumine, there's very little overhead to storing last traded prices in the strategy's "context" dictionary and overwriting each time they change with a market update while the market until the goes "inplay". This would be the recommended approach in most situations.
</details>

<details markdown="1">
<summary>The market start times don’t seem to match when events actually start in my country. Why is this?</summary>
Betfair datetimes are always for UTC timezone. You will need to translate them to your local timezone.
</details>

<details markdown="1">
<summary>How do I get the trap number for greyhounds in Betfair?</summary>
The trap number is always the first character (and very, very rarely, first two characters) of the runner name. So if ```Droopys Child``` is running in trap 3, the selection name in the market catalogue will be ```3. Droopys Child```. If that dog is a non-runner, then for UK greyhounds it might be replaced by ```3. Flopsy Bunny (res)```. for Australian greyhounds the replacement might be ```9. Flopsy Bunny```, with sort priority 3.
</details>

<details markdown="1">
<summary>I see that I can download or buy historic data from Betfair. Is it worth it?</summary>
Betfair historic data comes in three flavours: basic, advanced and pro. The [specification for each package is available here](https://historicdata.betfair.com/Betfair-Historical-Data-Feed-Specification.pdf). Basic data is free but generally of little value as it contains the last traded price at minute intervals and little else. Advanced offers data on best prices and volumes, but with a 1 second frequency only. Pro data is intended to provide the same content and granularity as if you were streaming it yourself.

The channel's most common recommendation is to stream and collect it yourself. If you have time cnstraints or deep pockets, the most common  recommendation is not to both with Advanced data and use only the Pro version to most closely replicate what you will encounter with your live strategies.

If you are considering purchasing data, checkout what [free advanced and pro data](https://historicdata.betfair.com/#/home) Betfair might have available from time-to-time</a>.
</details>

<details markdown="1">
<summary>Where can I get historic Betfair starting prices?</summary>
Historic BSPs are only available for horse and greyhound racing and can be found [here](https://promo.betfair.com/betfairsp/prices), though the data is sometimes incomplete.
</details>

## Third Party Data

<details markdown="1">
<summary>Where can I get historic starting prices for football matches?</summary>
Betfair doesn't currently have starting prices for football (soccer) matches. However, bookmaker starting prices (aka market closing prices) for major leagues are available for free from [football-data.co.uk](https://football-data.co.uk/) and a host of paid-for services.
</details>

<details markdown="1">
<summary>I’ve been using a third-party soccer feed, but the team names don’t always match with Betfair's. How do other people handle this?</summary>
There's no easy answer to this. Many people build and maintain mappings between the data provided by different providers.

These mappings may be created manually or programmatically by using a combination of event matching and fuzzy text matching. So for example, by matching all soccer teams in the same league, or all the horses in the same race, you will usually be left with only a very small number that don't match easily.
Althouh not a direct answer to your question, you may also find [these Betfair mappings](https://github.com/mberk/betfairmappings) helpful.
</details>
