# Changeable Prefixes

## How To Use

* Add your bot token to line 21 of [main.py](https://github.com/NexInfinite/DiscordBotHelp/tree/a3607068536fa4e82d8902c21ed6762dad9ff144/Changeable%20Prefixes/main.py). This can be obtained by following [this](https://discordpy.readthedocs.io/en/latest/discord.html).
* Change the `default_prefix` on line 6 of [main.py](https://github.com/NexInfinite/DiscordBotHelp/tree/a3607068536fa4e82d8902c21ed6762dad9ff144/Changeable%20Prefixes/main.py) \(optional\).
* Run the script.

When the bot joins a guild it will add the prefix to a json file; it will use the `default_prefix` to assign a prefix.

## Usage

### Syntax

`!prefix <new_prefix>`

### Example

`!prefix ?`

![prefix](https://i.gyazo.com/76a7cfb3c7945aaeb99c5f275da16803.png)

## Files

### main.py

```python
import json

from discord.ext import commands


default_prefix = "!"


def get_prefix(client, message):
    with open("prefixes.json") as f:
        prefixes = json.load(f)

    try:
        prefix = prefixes[str(message.guild.id)]
    except KeyError:
        prefix = default_prefix

    return prefix


TOKEN = "YOUR TOKEN"
bot = commands.Bot(command_prefix=get_prefix)

startup_extensions = ["Cog.prefix"]


@bot.event
async def on_guild_join(guild):
    with open("prefixes.json") as f:
        prefixes = json.load(f)

    prefixes[str(guild.id)] = default_prefix

    with open("prefixes.json", "w") as f:
        json.dump(prefixes, f, indent=2)


@bot.event
async def on_guild_remove(guild):
    with open("prefixes.json") as f:
        prefixes = json.load(f)

    prefixes.pop(str(guild.id))

    with open("prefixes.json", "w") as f:
        json.dump(prefixes, f, indent=2)


@bot.event
async def on_ready():
    # On read, after startup
    print(f"Connecting...\nConnected {bot.user}\n")  # Send message on connected


if __name__ == "__main__":  # When script is loaded, this will run
    bot.remove_command("help")
    for extension in startup_extensions:
        try:
            bot.load_extension(extension)  # Loads cogs successfully
        except Exception as e:
            exc = '{}: {}'.format(type(e).__name__, e)
            print('Failed to load extension {}\n{}'.format(extension, exc))  # Failed to load cog, with error

bot.run(TOKEN)  # Run bot with token
```

### Cog/prefix.py

```python
import discord
from discord.ext import commands

from json_commands import *


class Prefix(commands.Cog, name="Prefix"):
    def __init__(self, bot):
        self.bot = bot

    @commands.command(name="prefix")
    async def change_prefix(self, ctx, *, new_prefix: str = None):
        """Change the prefix of the bot for your server."""
        with open("prefixes.json") as f:
            prefixes = json.load(f)

        if new_prefix is None:
            embed = discord.Embed(color=discord.Color.dark_blue())
            embed.set_author(name=f"The prefix is {prefixes[str(ctx.guild.id)]}")
            await ctx.send(embed=embed)
        else:
            prefixes[str(ctx.guild.id)] = new_prefix

            with open("prefixes.json", "w") as f:
                json.dump(prefixes, f, indent=2)

            embed = discord.Embed(description=f"The new prefix is `{new_prefix}`",
                                  color=discord.Color.dark_blue())
            embed.set_author(name=f"Prefix changed!")
            await ctx.send(embed=embed)


def setup(bot):
    bot.add_cog(Prefix(bot))
```

### prefixes.json

```javascript
{
  
}
```

### File hierarchy 

* Cog
  * prefix.py
* prefixes.json
* main.py

