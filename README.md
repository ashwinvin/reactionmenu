![logo](https://cdn.discordapp.com/attachments/655186216060321816/820162226316378162/discord.jpg)
<div align="center">

[![Downloads](https://pepy.tech/badge/reactionmenu)](https://pepy.tech/project/reactionmenu) [![Downloads](https://pepy.tech/badge/reactionmenu/month)](https://pepy.tech/project/reactionmenu) [![Downloads](https://pepy.tech/badge/reactionmenu/week)](https://pepy.tech/project/reactionmenu)
</div>

## Github Updates vs PyPI Updates
The Github version of this package will always have the latest changes, fixes, and additions before the [PyPI](https://pypi.org/project/reactionmenu/) version. You can install the Github version by doing:
```
pip install git+https://github.com/Defxult/reactionmenu.git
```
You must have [Git](https://git-scm.com/) installed in order to do this

---
## How to install
```
pip install reactionmenu
```
## How to import
```py
from reactionmenu import ReactionMenu, Button, ButtonType
```
## Showcase
![showcase](https://cdn.discordapp.com/attachments/655186216060321816/819885696176226314/showcase.gif)

This package comes with several methods and options in order to make a discord reaction menu simple. Once you have imported the proper classes, you will initialize the constructor like so:
```py
menu = ReactionMenu(ctx, back_button='◀️', next_button='▶️', config=ReactionMenu.STATIC) 
```
---
## Parameters of the ReactionMenu constructor
* `ctx` The `discord.ext.commands.Context` object
* `back_button` Emoji used to go to the previous page ([supported emojis](#supported-emojis))
* `next_button` Emoji used to go to the next page ([supported emojis](#supported-emojis))
* `config` The config of the menu is important. You have two options when it comes to configuration. 
    * `ReactionMenu.STATIC` [more info](#reactionmenustatic-vs-reactionmenudynamic)
    * `ReactionMenu.DYNAMIC` [more info](#reactionmenustatic-vs-reactionmenudynamic)
---
## Options of the ReactionMenu constructor [kwargs]
| Name | Type | Default Value | Used for | Info
|------|------|---------------|----------|-------
| `rows_requested` | `int` |`None` | `ReactionMenu.DYNAMIC` | [more info](#dynamic)
| `custom_embed` | `discord.Embed` | `None` | `ReactionMenu.DYNAMIC` | [more info](#dynamic)
| `wrap_in_codeblock` | `str` | `None` | `ReactionMenu.DYNAMIC` | [more info](#dynamic)
| `clear_reactions_after` | `bool` | `True` | `STATIC and DYNAMIC` | delete all reactions after the menu times out
| `timeout` | `float` | `60.0` | `STATIC and DYNAMIC` | timer in seconds for when the menu ends
| `show_page_director` | `bool` | `True` | `STATIC and DYNAMIC` | show/do not show the current page in the embed footer (Page 1/5)
| `name` | `str` | `None` | `STATIC and DYNAMIC` | name of the menu instance
| `style` | `str` | `Page 1/X` | `STATIC and DYNAMIC` | custom page director style
| `all_can_react` | `bool` | `False` | `STATIC and DYNAMIC` | if all members can navigate the menu or only the message author
| `delete_interactions` | `bool` | `True` | `STATIC and DYNAMIC` | delete the bot prompt message and the users message after selecting the page you'd like to go to when using `ButtonType.GO_TO_PAGE`
| `navigation_speed` | `str` | `ReactionMenu.NORMAL` | `STATIC and DYNAMIC` | sets if the user needs to wait for the reaction to be removed by the bot before "turning" the page. Setting the speed to `ReactionMenu.FAST` makes it so that there is no need to wait (reactions are not removed on each press) and can navigate lengthy menu's more quickly
| `delete_on_timeout` | `bool` | `False` | `STATIC and DYNAMIC` | When the menu times out, delete the menu message. This overrides `clear_reactions_after`
> NOTE: All `ReactionMenu` kwargs can also be set using an instance of `ReactionMenu` **except** `rows_requested`
---
## ReactionMenu.STATIC vs ReactionMenu.DYNAMIC
## Static 
A static menu is used when you have a known amount of embed pages you would like to add to the menu
* Associated methods
    * `ReactionMenu.add_page(embed: Embed)`
    * `ReactionMenu.remove_page(page_number: int)`
    * `ReactionMenu.clear_all_pages()`
    * `ReactionMenu.clear_all_custom_pages()`

##### Adding Pages
```py
menu = ReactionMenu(ctx, back_button='◀️', next_button='▶️', config=ReactionMenu.STATIC)
menu.add_page(greeting_embed)
menu.add_page(goodbye_embed)

# NOTE: it can also be used in a loop
member_details = [] # contains embed objects
for member_embed in member_details:
    menu.add_page(member_embed)
```
##### Deleting Pages
You can delete a single page using `menu.remove_page()` or all pages with `menu.clear_all_pages()`. If you have any custom embed pages ( [more on that below](#all-buttontypes) ), you can delete them all with `menu.clear_all_custom_pages()`

## Dynamic
A dynamic menu is used when you do not know how much information will be applied to the menu. For example, if you were to request information from a database, that information can always change. You query something and you might get 1,500 results back, and the next maybe only 800. A dynamic menu pieces all this information together for you and adds it to an embed page by rows of data. `.add_row()` is best used in some sort of `Iterable` where everything can be looped through, but only add the amount of data you want to the menu page.
> NOTE: In a dynamic menu, all added data is placed in the description section of an embed. If you choose to use a `custom_embed`, all text in the description will be overriden with the data you add
* Associated methods
    * `ReactionMenu.add_row(data: str)`
    * `ReactionMenu.clear_all_row_data()`
    * `ReactionMenu.set_main_pages(*embeds: Embed)`
    * `ReactionMenu.set_last_pages(*embeds: Embed)`
* The kwargs specifically made for a dynamic menu are:
    * `rows_requested` - The amount of rows you would like on each embed page before making a new page
        * `ReactionMenu(ctx, ..., rows_requested=5)`
    * `custom_embed` - An embed you have created to use as the embed pages. Used for your menu aesthetic
        * `ReactionMenu(ctx, ..., custom_embed=red_embed)`
    * `wrap_in_codeblock` - The language identifier when wrapping your data in a discord codeblock. 
        * `ReactionMenu(ctx, ..., wrap_in_codeblock='py')`

##### Adding Rows/data
```py
menu = ReactionMenu(ctx, back_button='◀️', next_button='▶️', config=ReactionMenu.DYNAMIC, rows_requested=2)

for my_data in database.request('SELECT * FROM customers'):
    menu.add_row(my_data)

# NOTE: you can also add rows manually 
menu.add_row('Have a')
menu.add_row('great')
menu.add_row('day!')
```
##### Deleting Data
You can remove all the data you've added to a menu by using `menu.clear_all_row_data()`

##### Main/Last Pages
When using a dynamic menu, the only embed pages you see are from the data you've added. But if you would like to show more pages other than just the data, you can use methods `.set_main_pages` and `.set_last_pages`. Setting the main page(s), the embeds you set will be the first embeds that are shown when the menu starts. Setting the last page(s) are the last embeds shown
```py
menu.set_main_pages(welcome_embed, announcement_embed)

for data in get_information():
    menu.add_row(data)

menu.set_last_pages(additonal_info_embed)
# NOTE: setting main/last pages can be set in any order
```
---
## What are Buttons and ButtonTypes?
Buttons/button types are used when you want to add a reaction to the menu that does a certain function. Buttons and button types work together to achieve the desired action.

##### Parameters of the Button constructor
* `emoji` The emoji you would like to use as the reaction
* `linked_to` When the reaction is clicked, this is what determines what it will do (`ButtonType`)

##### Supported emojis
The emoji parameter supports all forms of emojis. 
```py
Button(emoji='😄' , ...)
Button(emoji='<:miscTwitter:705423192818450453>', ...)
Button(emoji='\U000027a1', ...)
Button(emoji='\N{winking face}', ...)

# NOTE: These formats are applicable to the ReactionMenu back and next buttons
```

##### Options of the Button constructor [kwargs]
| Name | Type | Default Value | Used for
|------|------|---------------|----------
| `name` | `str` |`None` | The name of the button object
| `embed` | `discord.Embed` | `None` | When the reaction is pressed, go to the specifed embed. 
| `details` | [more info](#buttons-with-buttontypecaller) | `None` | Assigns the function and it's arguments to call when a `Button` with `ButtonType.CALLER` is pressed

> NOTE: All `Button` kwargs can also be set using an instance of `Button`

* Associated methods
    * `ReactionMenu.add_button(button: Button)`
    * `ReactionMenu.clear_all_buttons()`
    * `ReactionMenu.remove_button(identity: Union[str, Button])`
    * `ReactionMenu.change_appear_order(*emoji_or_button: Union[str, Button])`
    * `ReactionMenu.get_button_by_name(name: str)`
    * `ReactionMenu.help_appear_order()`
    * `ButtonType.caller_details(func, *args, **kwargs)`

##### All ButtonTypes
| Type | Info |
|-------|------|
| `ButtonType.NEXT_PAGE` | Go to the next page in the menu session
| `ButtonType.PREVIOUS_PAGE` | Go to the previous page in the menu session
| `ButtonType.GO_TO_FIRST_PAGE` | Go to the first page in the menu session
| `ButtonType.GO_TO_LAST_PAGE` | Go to the last page in the menu session
| `ButtonType.GO_TO_PAGE` | Prompts you to type in the page you'd like to go to
| `ButtonType.END_SESSION` | Terminates the session and deletes the menu message. This is not like `ReactionMenu.stop()`
| `ButtonType.CUSTOM_EMBED` | Used separately from the navigation buttons. Once clicked, go to the specified embed 
| `ButtonType.CALLER` | Used when specifying the function to call and it's arguments when the button is pressed ( [more info](#buttons-with-buttontypecaller) )

##### Adding Buttons
You can add buttons (reactions) to the menu using a `Button`. By default, two buttons have already been set in the `ReactionMenu` constructor. The `back_button` as `ButtonType.PREVIOUS_PAGE` and `next_button` as `ButtonType.NEXT_PAGE`. It's up to you if you would like additional buttons. Below are examples on how to implement each `ButtonType`. 
> NOTE: Buttons with `ButtonType.CALLER` are a little different, so there is a dedicated section explaining how they work and how to implement them [here](#buttons-with-buttontypecaller)


```py
menu = ReactionMenu(...)

# first and last pages
fpb = Button(emoji='⏪', linked_to=ButtonType.GO_TO_FIRST_PAGE)
lpb = Button(emoji='⏩', linked_to=ButtonType.GO_TO_LAST_PAGE)

# go to page
gtpb = Button(emoji='🔢', linked_to=ButtonType.GO_TO_PAGE)

# end session
esb = Button(emoji='❌', linked_to=ButtonType.END_SESSION)

# custom embed
ceb = Button(emoji='😎', linked_to=ButtonType.CUSTOM_EMBED, embed=discord.Embed(title='Hello'))

menu.add_button(fpb)
menu.add_button(lpb)
menu.add_button(gtpb)
menu.add_button(esb)
menu.add_button(ceb)
```
##### Deleting Buttons
Remove all buttons with `menu.clear_all_buttons()`. You can also remove an individual button using its name if you have it set, or the button object itself with `menu.remove_button()`

##### Buttons with ButtonType.CALLER
`ButtonType.CALLER` buttons are used to implement your own functionality into the menu. Maybe you want to add a button that creates a text channel, sends a message, or add something to a database, whatever it may be. In order to work with `ButtonType.CALLER`, use the class method below.

* `ButtonType.caller_details(func, *args, **kwargs)`
  
This class method is used to setup a function and it's arguments that are later called when the button is pressed. The `Button` constructor has the kwarg `details`, and that's what you'll use with `.caller_details` to assign the values needed. Some examples are below on how to properly implement `ButtonType.CALLER`

```py
@client.command()
async def user(ctx, name, *, message):
    await ctx.send(f"Hi {name}! {message}. We're glad you're here!")

def car(year, make, model):
    print(f"I have a {year} {make} {model}")

ub = Button(emoji='👋', linked_to=ButtonType.CALLER, details=ButtonType.caller_details(user, ctx, 'Defxult', message='Welcome to the server'))
cb = Button(emoji='🚗', linked_to=ButtonType.CALLER, details=ButtonType.caller_details(car, 2021, 'Ford', 'Mustang'))
```
> NOTE: The function you pass in should not return anything. Calling functions with `ButtonType.CALLER` does not store or handle anything returned by that function

---

##### Emoji Order
It is possible to change the order the reactions appear in on the menu.
```py
first_button = Button(emoji='⏪', linked_to=ButtonType.GO_TO_FIRST_PAGE)
close_menu_button = Button(emoji='❌', linked_to=ButtonType.END_SESSION, name='end')

# NOTE 1: When changing the order, you need to include the default back and next buttons because they are there by default. Access the default back/next buttons with menu attributes
# NOTE 2: You can use the emoji or button object 

menu.change_appear_order(first_button, menu.default_back_button, close_menu_button, menu.default_next_button)
```
If you did not make an instance of a Button object to access, you can still get that button object by its name if it is set and has been added to the menu via `menu.add_button()`. Example: `menu.get_button_by_name('end')`. With the helper function `menu.help_appear_order()`, it simply prints out all active buttons to the console so you can copy and paste each emoji in the order you'd like.

---
## Setting Limits
* Associated CLASS Methods
    * `ReactionMenu.set_sessions_limit(limit: int, message: str)` 
    * `ReactionMenu.cancel_all_sessions()`
    * `ReactionMenu.get_sessions_count()`

If you'd like, you can limit the amount of reaction menus that can be active at the same time. You can do this by using the class method above. Example:
```py
from discord.ext import commands
from reactionmenu import ReactionMenu, Button, ButtonType

class Example(commands.Cog):
    def __init__(self, bot):
        self.bot = bot
	ReactionMenu.set_sessions_limit(3, 'Sessions are limited')
```

With the above example, only 3 menus can be active at once, and if someone tries to create more before other menus are finished, they will get an error message saying "Sessions are limited".

Depending on where you host your bot, having an excess amount of menus running can possibly have an impact on your bots performance. It is possible to cancel all sessions. Example:
```py
@commands.command()
@commands.has_role('Admin')
async def killsessions(self, ctx):
    # ...
    ReactionMenu.cancel_all_sessions()
```

---
## Starting/Stopping the Menu
* Associated Methods
    * `await ReactionMenu.start(*, send_to=None)`
    * `await ReactionMenu.stop(*, delete_menu_message=False, clear_reactions=False)`

When starting the menu, you have the option to send the menu to a certain channel. Parameter `send_to` is the channel you'd like to send the menu to. You can set `send_to` as the channel name (`str`), channel ID (`int`), or channel object (`discord.TextChannel`). Example:
```py
menu = ReactionMenu(...)
# channel name
await menu.start(send_to='bot-commands')

# channel ID
await menu.start(send_to=1234567890123456)

# channel object
channel = guild.get_channel(1234567890123456)
await menu.start(send_to=channel)
```

When stopping the menu, you have two options. Delete the reaction menu by setting the first parameter to `True` or only remove all it's reactions, setting the second parameter to `True`

---
#### All Attributes
| Attribute | Return Type | Info 
|-----------|-------------|----------
| `ReactionMenu.STATIC` | `int` | menu config value (class attribute)
| `ReactionMenu.DYNAMIC` | `int` | menu config value (class attribute)  
| `ReactionMenu.NORMAL` | `str` | menu kwarg value (class attribute)
| `ReactionMenu.FAST` | `str` | menu kwarg value (class attribute)
| `ReactionMenu.config` | `int` | menu config value (`STATIC` or `DYNAMIC`)
| `ReactionMenu.is_running` | `bool` | if the menu is currently active
| `ReactionMenu.default_next_button` | `Button` | default next button (in the `ReactionMenu` constructor)
| `ReactionMenu.default_back_button` | `Button` | default back button (in the `ReactionMenu` constructor)
| `ReactionMenu.next_buttons` | `List[Button]` | all active `ButtonType.NEXT_PAGE` buttons
| `ReactionMenu.back_buttons` | `List[Button]` | all active `ButtonType.PREVIOUS_PAGE` buttons
| `ReactionMenu.first_page_buttons` | `List[Button]` | all active `ButtonType.GO_TO_FIRST_PAGE` buttons
| `ReactionMenu.last_page_buttons` | `List[Button]` | all active `ButtonType.GO_TO_LAST_PAGE` buttons
| `ReactionMenu.end_session_buttons` | `List[Button]` | all active `ButtonType.END_SESSION` buttons
| `ReactionMenu.custom_embed_buttons` | `List[Button]` | all active `ButtonType.CUSTOM_EMBED` buttons
| `ReactionMenu.go_to_page_buttons` | `List[Button]` | all active `ButtonType.GO_TO_PAGE` buttons
| `ReactionMenu.caller_buttons` | `List[Button]` | all active `ButtonType.CALLER` buttons
| `ReactionMenu.all_buttons` | `List[Button]` | all active buttons
| `ReactionMenu.rows_requested` | `int` | the amount of rows you have set to request
| `ReactionMenu.timeout` | `float` | value in seconds of when the menu ends
| `ReactionMenu.show_page_director` | `bool` | show/do not show the current page on the embed
| `ReactionMenu.name` | `str` | name of the menu instance
| `ReactionMenu.style` | `str` | custom page director style
| `ReactionMenu.all_can_react` | `bool`  | if all members can navigate the menu or only the message author
| `ReactionMenu.custom_embed` | `discord.Embed` | embed object used for custom pages
| `ReactionMenu.wrap_in_codeblock` | `str` | language identifier when wrapping your data in a discord codeblock
| `ReactionMenu.total_pages` | `int` | total amount of built pages
| `ReactionMenu.delete_interactions` | `bool` | delete the bot prompt message and the users message after selecting the page you'd like to go to when using `ButtonType.GO_TO_PAGE`
| `ReactionMenu.navigation_speed` | `str` | the current setting for the menu navigation speed
| `ReactionMenu.delete_on_timeout` | `bool` | if the menu message will delete upon timeout

<!-- ---
#### Bug Reports & Suggestions
If you find any bugs or have any suggestions for the package, please submit them on [Github](https://github.com/Defxult/reactionmenu/issues) =) -->