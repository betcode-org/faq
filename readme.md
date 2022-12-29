# Betcode FAQ

This is FAQ draws from and supplements the discussions that take place in the Betcode Slack channel.

It is designed to be used with the mkdocs documentation site generation tool, which Python users can install locally with ```pip install mkdocs```. Once installed, browse to folder where this repository has been closed and initiate an mkdocs server ```python -m mkdocs serve```. It will provide a localhost port number where the site may be viewed.

Use of a site generation tool saves an enormous amount of time, but inevitably at the cost of some restrictions. According all q&A pairs are wrapped in a ```<details markdown="1">``` tag. The tag attribute is important as it allows the markdown inside each block to be properly processed.

Inside the block, however much you want to prettify your html by indenting, resist. The markdown interpretor will conclude that this is code and the results can be downright ugly.
