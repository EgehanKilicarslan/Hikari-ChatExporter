<div align="center">

[![Version][pypi-version]][pypi-url]
[![Language][language-dom]][github-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![GPL License][license-shield]][license-url]


  <h2>Hikari-ChatExporter</h2>

  <p>
    Export Discord chats with your hikari bots!
    <br />
</p>
</div>

---
## Installation

To install the library to your virtual environment, for bot usage, run the command:
```sh 
pip install hikari-chat-exporter
```

To clone the repository locally, run the command:
```sh
git clone https://github.com/EgehanKilicarslan/Hikari-ChatExporter
```

<p align="right">(<a href="#top">back to top</a>)</p>

---
## Usage

There are currently 3 methods (functions) to `chat-exporter` which you can use to export your chat.<br/>
_Expand the blocks below to learn the functions, arguments and usages._
<details><summary><b>Basic Usage</b></summary>

`.quick_export()` is the simplest way of using chat-exporter.

Using the _quick_export_ function will gather the history of the channel you give, build the transcript then post the file and embed directly to the channel - returning a message object gathered from the message it posted.

This is mostly seen as a demo function, as opposed to a command you should actually use. 

**Required Argument(s):**<br/>
`channel`: `hikari.channels.PartialChannel` object, whether `ctx.channel` or any channel you gather.

**Optional Argument(s):**<br/>
`bot`: `commands.Bot` object to gather members who are no longer in your guild.

**Return Argument:**<br/>
`hikari.messages.Message`: The message _quick_export_ will send, containing the embed and exported chat file.

**Example:**
```python
import hikari
import lightbulb
import chat_exporter


bot = hikari.GatewayBot(token="...")
client = lightbulb.client_from_app(bot)
bot.subscribe(hikari.StartingEvent, client.start)

...

@client.register
class Save(
    lightbulb.SlashCommand, 
    name="save",
    description="Saves current chat transcript."
):
    @lightbulb.invoke
    async def invoke(self, ctx: lightbulb.Context) -> None:
        await chat_exporter.quick_export(ctx.interaction.get_channel())
        await ctx.respond("Transcript created!")

...
```

</details>

<details><summary><b>Customisable Usage</b></summary>

`.export()` is the most efficient and flexible method to export a chat using chat-exporter.

Using the _export_ function will generate a transcript using the channel you pass in, along with using any of the custom kwargs passed in to set limits, timezone, 24h formats and more (listed below).

This would be the main function to use within chat-exporter.

**Required Argument(s):**<br/>
`channel`: `hikari.channels.PartialChannel` object, whether `ctx.channel` or any channel you gather.

**Optional Argument(s):**<br/>
`limit`: Integer value to set the limit (amount of messages) the chat exporter gathers when grabbing the history (default=unlimited).<br/>
`tz_info`: String value of a [TZ Database name](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List) to set a custom timezone for the exported messages (default=UTC)<br/>
`military_time`: Boolean value to set a 24h format for times within your exported chat (default=False | 12h format)<br/>
`fancy_times`: Boolean value which toggles the 'fancy times' (Today|Yesterday|Day)<br/>
`bot`: `commands.Bot` object to gather members who are no longer in your guild.

**Return Argument:**<br/>
`transcript`: The HTML build-up for you to construct the HTML File with Discord.

**Example:**
```python
import io
import hikari
import lightbulb
import chat_exporter


bot = hikari.GatewayBot(token="...")
client = lightbulb.client_from_app(bot)
bot.subscribe(hikari.StartingEvent, client.start)

...

@client.register
class Save(
    lightbulb.SlashCommand, 
    name="save",
    description="Saves current chat transcript."
):
    @lightbulb.invoke
    async def invoke(self, ctx: lightbulb.Context) -> None:
        limit: int = 100
        tz_info: str = "UTC"
        military_time: bool = True
        channel = ctx.interaction.get_channel()

        transcript = await chat_exporter.export(
            channel,
            limit=limit,
            tz_info=tz_info,
            military_time=military_time,
            bot=bot
        )

        if transcript is None:
            return

        transcript_file = hikari.files.Bytes(io.BytesIO(transcript.encode()), f"transcript-{channel.name}.html")

        await ctx.respond(transcript_file)
```
</details>
<details><summary><b>Raw Usage</b></summary>

`.raw_export()` is for the crazy people who like to do their own thing when using chat-exporter.

Using the _raw_export_ function will generate a transcript using the list of messages you pass in, along with using any of the custom kwargs passed in to set limits, timezone, 24h formats and more (listed below).

This would be for people who want to filter what content to export.

**Required Argument(s):**<br/>
`channel`: `hikari.channels.PartialChannel` object, whether `ctx.channel` or any channel you gather (this is just for padding the header).<br/>
`messages`: A list of Message objects which you wish to export to an HTML file.

**Optional Argument(s):**<br/>
`tz_info`: String value of a [TZ Database name](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List) to set a custom timezone for the exported messages (default=UTC)<br/>
`military_time`: Boolean value to set a 24h format for times within your exported chat (default=False | 12h format)<br/>
`fancy_times`: Boolean value which toggles the 'fancy times' (Today|Yesterday|Day)<br/>
`bot`: `commands.Bot` object to gather members who are no longer in your guild.

**Return Argument:**<br/>
`transcript`: The HTML build-up for you to construct the HTML File with Discord.

**Example:**
```python
import io
import hikari
import lightbulb
import chat_exporter


bot = hikari.GatewayBot(token="...")
client = lightbulb.client_from_app(bot)
bot.subscribe(hikari.StartingEvent, client.start)

...

@client.register
class Save(
    lightbulb.SlashCommand, 
    name="save",
    description="Saves current chat transcript."
):
    @lightbulb.invoke
    async def invoke(self, ctx: lightbulb.Context) -> None:
        tz_info: str = "UTC"
        military_time: bool = True
        channel = ctx.interaction.get_channel()
        messages = bot.rest.fetch_messages(channel)

        transcript = await chat_exporter.raw_export(
            channel,
            messages=messages,
            tz_info=tz_info,
            military_time=military_time,
            bot=bot
        )

        if transcript is None:
            return

        transcript_file = hikari.files.Bytes(io.BytesIO(transcript.encode()), f"transcript-{channel.name}.html")

        await ctx.respond(transcript_file)
```
</details>

<p align="right">(<a href="#top">back to top</a>)</p>

---
## Screenshots

<details><summary><b>General</b></summary>
<ol>
    <details><summary>Discord</summary>
    <img src="https://raw.githubusercontent.com/EgehanKilicarslan/Hikari-ChatExporter/master/.screenshots/channel_output.png">
    </details>
    <details><summary>Chat-Exporter</summary>
    <img src="https://raw.githubusercontent.com/EgehanKilicarslan/Hikari-ChatExporter/master/.screenshots/html_output.png">
    </details>
</ol>
</details>
<p align="right">(<a href="#top">back to top</a>)</p>


---
## Additional Functions

<details><summary><b>Link Function</b></summary>
Downloading exported chats can build up a bunch of unwanted files on your PC which can get annoying, additionally - not everyone wants to download content from Discord.

Due to these pain, and many requests - I have built a fancy PHP script which will show the transcript file within a browser.<br/>
<ol>
<details><summary>quick_link</summary>
Similar in design to `.quick_export()` this is a bit of a demo function to produce a link and to give you an embed.

**Required Argument(s):**<br/>
`channel`: `hikari.channels.PartialChannel` object, whether `ctx.channel` or any channel you gather.<br/>
`message`: The Discord message containing the transcript file

**Return Argument:**<br/>
`hikari.messages.Message`: The message _quick_link_ will send, containing the embed.

**Example:**
```python
import hikari
import lightbulb
import chat_exporter


bot = hikari.GatewayBot(token="...")
client = lightbulb.client_from_app(bot)
bot.subscribe(hikari.StartingEvent, client.start)

...

@client.register
class Save(
    lightbulb.SlashCommand, 
    name="save",
    description="Saves current chat transcript."
):
    @lightbulb.invoke
    async def invoke(self, ctx: lightbulb.Context) -> None:
        channel = ctx.interaction.get_channel()
        message = await chat_exporter.quick_export(channel)
        await chat_exporter.quick_link(channel, message)
```
</details>

<details><summary>link</summary>
A simple function to return the link you will need to view the transcript online.

**Required Argument(s):**<br/>
`message`: The Discord message containing the transcript file

**Return Argument:**<br/>
`link`: The link to view the transcript file online

**Example:**
```python
import io
import hikari
import lightbulb
import chat_exporter


bot = hikari.GatewayBot(token="...")
client = lightbulb.client_from_app(bot)
bot.subscribe(hikari.StartingEvent, client.start)

...

@client.register
class Save(
    lightbulb.SlashCommand, 
    name="save",
    description="Saves current chat transcript."
):
    @lightbulb.invoke
    async def invoke(self, ctx: lightbulb.Context) -> None:
        channel = ctx.interaction.get_channel()
        transcript = await chat_exporter.export(channel)
    
        if transcript is None:
            return

        transcript_file = hikari.File(
            io.BytesIO(transcript.encode()),
            filename=f"transcript-{channel.name}.html",
        )

        message = await ctx.respond(attachment=transcript_file)
        link = await chat_exporter.link(message)

        await ctx.respond("Click this link to view the transcript online: " + link)
```
</details>
</ol>

_Please note that the PHP script does NOT store any information.<br/>
It simply makes a request to the given URL and echos (prints) the content for you to be able to view it._

</details>

---
## Attributions

*This project borrows CSS and HTML code from [Tyrrrz's C# DiscordChatExporter](https://github.com/Tyrrrz/DiscordChatExporter/) repository.*

*This project is based on [DiscordChatExporterPy](https://github.com/mahtoid/DiscordChatExporterPy), the work of [mahtoid](https://github.com/mahtoid).*
<p align="right">(<a href="#top">back to top</a>)</p>

<!-- LINK DUMP -->
[pypi-version]: https://img.shields.io/pypi/v/hikari-char-exporter?style=for-the-badge
[pypi-url]: https://pypi.org/project/hikari-char-exporter/
[language-dom]: https://img.shields.io/github/languages/top/EgehanKilicarslan/Hikari-ChatExporter?style=for-the-badge
[forks-shield]: https://img.shields.io/github/forks/EgehanKilicarslan/Hikari-ChatExporter?style=for-the-badge
[forks-url]: https://github.com/EgehanKilicarslan/Hikari-ChatExporter/
[stars-shield]: https://img.shields.io/github/stars/EgehanKilicarslan/Hikari-ChatExporter?style=for-the-badge
[stars-url]: https://github.com/EgehanKilicarslan/Hikari-ChatExporter/stargazers
[issues-shield]: https://img.shields.io/github/issues/EgehanKilicarslan/Hikari-ChatExporter?style=for-the-badge
[issues-url]: https://github.com/EgehanKilicarslan/Hikari-ChatExporter/issues
[license-shield]: https://img.shields.io/github/license/EgehanKilicarslan/Hikari-ChatExporter?style=for-the-badge
[license-url]: https://github.com/EgehanKilicarslan/Hikari-ChatExporter/blob/master/LICENSE
[github-url]: https://github.com/EgehanKilicarslan/Hikari-ChatExporter/
