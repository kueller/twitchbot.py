# twitchbot.py

This is not a full Twitch bot by itself, but rather a file one can use
as a base to make a quick bot in Python. Its purpose is to handle the
interfacing with Twitch's IRC API and have flexible enough features to
be adapted to different use cases users might want in a bot.

## TwitchBot Class

The TwitchBot class is all you will need to import to get a bot
running.  The other classes for IRC messages and tags, are created
internally through TwitchBot functions and only need to be read by the
user when applicable.  Each TwitchBot instance is designed for **one**
channel at a time.

TwitchBot implements Twitch's IRC API. For more details on what
certain functions actually do, it is best to review Twitch's own
documentation.

Twitch API reference:  
<https://github.com/justintv/Twitch-API/blob/master/IRC.md>

Twitch chat command reference:  
<https://help.twitch.tv/customer/en/portal/articles/659095-chat-commands>

### Introduction

The TwitchBot constructor takes no parameters. A standard setup of a TwitchBot
class will be as follows

```python
import twitchbot

bot = twitchbot.TwitchBot()
bot.setInfoFromConfig('config.txt')
bot.start()
```

More on the following two functions below.

### Available Variables

These are the public variables in the TwitchBot class.

Variable | Type | Default value
--- | --- | ---
`server` | string | `None`
`channel` | string | `None`
`username` | string | `None`
`password` | string | `None`
`subs_on` | boolean | `False`
`slow_on` | boolean | `False`
`r9k_on` | boolean | `False`
`emote_only_on` | boolean | `False`
`msg_channel_suspended` | boolean | `False`
`broadcaster_lang` | string | `''`
`userlist` | list | `[]`
`modlist` | list | `[]`

`server`, `channel`, `username`, and `password` can be written to
manually or by a config file (see: [Config File](#Config-File)). The
roomstate variables are updated internally, so it is not recommended
these be written to even if it's possible. `userlist` and `modlist`
are respectively lists of all the users and mods in the channel and
should also should not be modified manually.

### Functions

A quick link complete list of functions. See the long section
in the comprehensive [function list](#Function-List).

Initialization |
--- |
[`setInfoFromConfig`](#setInfoFromConfig) |
[`start`](#start) |
[`setPrintOptions`](#setPrintOptions) |

Chat interaction |
--- |
[`incoming`](#incoming) |
[`join`](#join) |
[`part`](#part) |
[`quitirc`](#quitirc) |
[`msg`](#msg) |
[`action`](#action) |

Chat commands (*See link: [Twitch chat commands](https://help.twitch.tv/customer/en/portal/articles/659095-chat-commands)*) |
--- |
[`color`](#color) |
[`ignore`](#ignore) |
[`unignore`](#unignore) |
[`timeout`](#timeout) |
[`purge`](#purge) |
[`ban`](#ban) |
[`unban`](#unban) |
[`clear`](#clear) |
[`slowon`](#slowon) |
[`slowoff`](#slowoff) |
[`subson`](#subson) |
[`subsoff`](#subsoff) |
[`r9kon`](#r9kon) |
[`r9koff`](#r9koff) |
[`emoteonlyon`](#emoteonlyon) |
[`emoteonlyoff`](#emoteonlyoff) |

User variables (*Main section: [User Variables](#User-Variables)*)|
--- |
[`getUserVar`](#getUserVar) |
[`setUserVar`](#setUserVar) |
[`newUserVar`](#newUserVar) |
[`delUserVar`](#delUserVar) |
[`getUserVarType`](#getUserVarType) |
[`setUserVarType`](#setUserVarType) |

Timer controls (*Main section: [Timers](#Timers)*) |
--- |
[`initializeTimers`](#initializeTimers) |
[`timersInitialized`](#timersInitialized) |
[`startTimers`](#startTimers) |
[`timersStarted`](#timersStarted) |
[`pauseTimers`](#pauseTimers) |
[`resumeTimers`](#resumeTimers) |
[`killTimers`](#killTimers) |
[`addTimer`](#addTimer) |
[`removeTimer`](#removeTimer) |
[`pauseTimer`](#pauseTimer) |
[`resumeTimer`](#resumeTimer) |
[`timerExists`](#timerExists) |

## IRCMessage Class

The IRCMessage class is a wrapper for a list of variables that parses
an IRC message. It is returned when calling the
[`incoming()`](#incoming) function. For the sake of consistency, every
variable is guaranteed to exist and have a default value of its
expected type. This makes it easier to do comparisons.  The only
exception is the tag, explained further down.

Since this is just a list of variables, there is no functions except the
initialization which is done internally by TwitchBot.

Variable | Description | Default Value |
--- | --- | --- |
`message` | The entire message sent. | `''`
`prefix` | Info of the user and server | `''`
`username` | Username of the sender | `''`
`host` | Host of the sender (same as `username` on Twitch). | `''`
`serv` | Chat server sender connected from. | `''`
`IRCcmd` | IRC code. Regular chat posts are "PRIVMSG". | `''`
`IRCparams` | Space separated list of all the components of the message. | `[]`
`body` | The actual text itself. The part you read in chat. | `''`
`command` | The first word of the body text. Easy to check for bot commands. | `''`
`argument` | Everything after the first word of the body text. Used for bot commands. | `''`
`tag` | IRCTag class if there is an IRCv3 tag. See more below. | `None`

Everything, except `tag` and `IRCparams`, will be a string.

### IRCTag Class

IRCTag is another variable based class like IRCMessage. It contains
information related to Twitch's IRCv3 tags. This can be helpful for
more Twitch specific information.

Like IRCMessage, this is created internally. If applicable, it can be
accessed from the IRCMessage `tag` variable.

For other descriptions and cases, see the IRCv3 documentation
starting here:  
<https://github.com/justintv/Twitch-API/blob/master/IRC.md#notice>

Twitch will give better documentation for the variables' purpose.
The names here are very close, if not exact, matches.

Variable | Type | Default Value
--- | --- | ---
`badges` | list | `[]`
`color` | string | `''`
`display_name` | string | `''`
`emotes` | string | `''`
`msg_id` | string | `''`
`isMod` | boolean | `False`
`isSub` | boolean | `False`
`isTurbo` | boolean | `False`
`room_id` | int | `0`
`user_id` | int | `0`
`user_type` | string | `''`
`bits` | int | `0`
`emote_sets` | list | `[]`
`broadcaster_lang` | string | `''`
`r9k` | boolean | `False`
`subs_only` | boolean | `False`
`slow` | int | `0`
`msg_param_months` | int | `0`
`system_msg` | string | `''`
`login` | string | `''`
`ban_duration` | int | `0`
`ban_reason` | string | `''`

There are a couple IRCTag variables exlusive to this class, for
convenience.

Variable | Type | Description | Default Value
--- | --- | --- | ---
`tags` | list | List of all the tags that were present in the message. | `[]`
`isCheer` | boolean | Tells if the post contains a cheer. | `False`


## User Variables

User variables are for any information that isn't included in the bot
by default.  There are no specific uses for these.  They can be state
variables, stored usernames, simple data structures, or even larger
structures like custom commands. The benefit is that because these variables are
stored internally by the bot they are attached to any TwitchBot instance
and can be passed around more easily.

### Handling User Variables

The user variables themselves are simply a dictionary that is kept
in the bot. Creating a user variable is a call to [`getUserVar`](#getUserVar),
passing in the variable's name as a string. The string name is the method
for handling all variables. Once created they can be set with any
value.

Variables can also be declared in the config file (see: [Config File](#Config-File)).
There are more limitations as to what kinds of variables can be created
in the file.

### Types

User variables contain a type property, but this is only
strictly enforced in the config file. Otherwise, consider variables
untyped. If you are creating variables through function calls, the
type value can be anything, and acts more as a general property
value.

### Function list

User Variables |
--- |
[`getUserVar`](#getUserVar) |
[`setUserVar`](#setUserVar) |
[`newUserVar`](#newUserVar) |
[`delUserVar`](#delUserVar) |
[`getUserVarType`](#getUserVarType) |
[`setUserVarType`](#setUserVarType) |

## Config File

TwitchBot has the ability to read basic information from a
configuration file. This is loading through the function
[`setInfoFromConfig()`](#setInfoFromConfig). What to call the
configuration file then is up to the user. In the configuration
file you can set the basic join information, declare and set
[user variables](#User-Variables) and set the
[command line print options](#setPrintOptions).

**Note:** Everything in the config file is case **insensitive**.

### Basic Information

Parameters are simple `name=value` format. There are three basic
initialization parameters: `channel`, `username`, and `password`.

Option | Description
-- | --
`channel` | This is the Twitch channel you want the bot to join. It *must* be prefaced with a '#'.
`username` | The username you want the bot to have.
`password` | Your oauth code to sign into Twitch. It must be the whole string beginning with 'oauth:'

These values can be set in software by manually setting the corresponding
bot variables (See: [Available Variables](#Available-Variables)). The same
rules mentioned above apply when setting the values manually. An absense of
any of these three variables will cause an error when calling
[`start()`](#start).

### Config User Variables: Declaration

User variables can be declared and assigned in the configuration file.
The declaration must occur before assignment. A declaration line
will be in the format

```
declare string/boolean/number variable_name
```

Variables in the config file are typed so they can be converted
properly when assigned to. A string will read a value as is. A boolean
will accept only "true" or "false" (case insensitive) when assigned.
A "number" can take both an integer or floating point value, and will
convert accordingly.

**Note:** Types only count for the configuration file. In software,
there is no type checking, and any assignment counts.

Variable names must start with a letter, and afterwards can contain
any combination of letters, numbers, or underscores, so long as the
name is not already declared. Once again, case insensitive.

### Config User Variables: Assignment

Below the declaration a user variable can be assigned to. If a variable
is not assigned to, its value will be left as `None` in software.
The assignment line will look like the following.

```
user.variable_name = value
```

The prefixing "user." will tell the parser this is a user variable and
not an invalid value. This seems extraneous, but in the future more
options are intended to be available under the "user" prefix. An error
is raised if the variable name does not exist, and the value is dependent
on the type specified in declaration.

### Printing Options

The print options can be set in the file. The format
is similar to user variables.

```
print.option = true/false
```

The `option` is any valid print option that would be passed into
the [`setPrintOptions()`](#setPrintOptions) function. See that section
for the documented list. It is assigned either true or false to set the
option.

### Example Config

```
declare boolean state
declare string current_game

channel=#test_channel
username=test_user
password=oauth:abcdefghijklmnop1234567890

user.state = true
user.current_game = Cat Planet

print.other = true
print.msg = true
```

## Timers

Because [`incoming()`](#incoming) is a blocking function, under a normal
loop there is no way to send periodic or timed messages with consistency.
TwitchBot then has a timer loop that runs internally in another thread.

### Setup

Timers are initialized with the
[`initializeTimers()`](#initializeTimers) function. This function is
**required** before any other timer functions can be called.

Timers can be added after the thread is initialized. However, the
timer loop will not actually *begin* until
[`startTimers()`](#startTimers) is called. Timers can continue to be
interfaced with after the loop has begun. It is recommended the timer
loop is not started until the bot has fully connected to the Twitch
channel, to make sure all messages are sent properly.

The thread is killed with the [`killTimers()`](#killTimers) function.
This should be called at the end, before the TwitchBot exits.

### Timer Format

Each timer has a specified delay, that can either be discrete or
chosen at random within a range. Timers can run once, or loop
infinitely. Each timer is identified by a code, which is needed
to interface with the timer after initialization. It is up to
the user to keep track of the timer codes.

### Callback Functions

Each timer is initiated with a callback function that is called when the
timer is triggered. A callback function will be any Python function
with a single parameter. The parameter can be anything, and is set on
timer initialization.

### Timer Functions

Timer controls |
--- |
[`initializeTimers`](#initializeTimers) |
[`timersInitialized`](#timersInitialized) |
[`startTimers`](#startTimers) |
[`timersStarted`](#timersStarted) |
[`pauseTimers`](#pauseTimers) |
[`resumeTimers`](#resumeTimers) |
[`killTimers`](#killTimers) |
[`addTimer`](#addTimer) |
[`removeTimer`](#removeTimer) |
[`pauseTimer`](#pauseTimer) |
[`resumeTimer`](#resumeTimer) |
[`timerExists`](#timerExists) |

### Example

```python
import twitchbot
import random

bot = twitchbot.TwitchBot()
bot.initializeTimers()

# In this case, the argument is a list of usernames.
def timer_callback(users):
   user = random.choice(users)
   bot.msg("Random user of the hour is %s!" % user)

t1_code = bot.addTimer("hr", 1, timer_callback, bot.userlist, loop=True)

bot.start()
bot.startTimers()
```

## Function List

Here is the full documented function list for the TwitchBot class.

---

#### setInfoFromConfig

```python
bot.setInfoFromConfig(filename)
```

This loads a configuration file and applies the appropriate values to the TwitchBot class.
See more about the file syntax and options in the [config file](#Config-File) section.

**Input**  
`filename`: Filename string of the config file to read from.

**Return**  
Nothing.

**Exceptions**  
`ValueError`, `KeyError`, or `IndexError` depending on the file syntax error.

---

#### start

```python
bot.start()
```

This function will connect to the server, set the bot username, and join the associated
channel.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
`ValueError` if either `bot.username`, `bot.channel`, or `bot.password` are `None`.

---

#### setPrintOptions

```python
bot.setPrintOptions(**kwargs)
```

This function determines what will be printed to stdout. It does not affect anything in the
TwitchChat. Any number of arguments can be passed, and their behavior is defined below.
**By default, TwitchBot has 'msg' and 'other' on**.

##### Print Options

All arguments are either `True` of `False`.

Arg | Behavior
--- | ---
msg | Sets options for printing messages of type PRIVMSG (standard chat posts). Does not print tags.
tag | Sets option for printing the IRCv3 tag
join | Sets option for if JOIN, PART, or QUIT are printed.
selfmsg | Sets if the bot will print its own messages to stdout.
none | Will print nothing to stdout. **This overrides any other option.**
other | Prints any message that isn't of type PRIVMSG, but isn't self. **Overrides all but 'msg' and 'none'.**
state | Prints any roomstate commands.
allmsg | Prints everything to stdout. **Overrides all but 'none'. Setting to False is equal to setting 'none'.**

##### Example

```python
bot.setPrintOption(msg=True, selfmsg=True, join=False)
```

**Input**  
Any set of print option arguments.

**Return**  
Nothing.

**Exceptions**  
`TypeError` if argument value is not of type `bool`.

---

#### incoming

```python
bot.incoming()
```

This is the function to receive messages from the server. A normal bot will have a loop
repeatedly calling `incoming()` and handling the return. PING sends are handled
automatically. User joins and leaves are also handled to add people to the userlist and modlist.
Roomstates also update.

Any messages are printed to the stdout in a manner defined by the print options.

The function return an [IRCMessage class](#IRCMessage-Class). See more in the related section.

**Note:** This is a *blocking* function. It will wait until the next message, whenever that is.
To print periodic statements irrelevant of `incoming()`, refer to the [Timers](#Timers) section.

**Input**  
Nothing.

**Return**  
New IRCMessage class

**Exceptions**  
Nothing.

---

#### join

```python
bot.join(channel)
```

Joins the channel specified as a parameter. Channel must begin with '#'.

**Input**    
`channel`: Channel string.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---
#### part
```python
bot.part(channel)
```

Leaves the specified channel. Channel must begin with '#'.

**Input**  
`channel`: Channel string.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### quitirc

```python
bot.quitirc(message)
```

This quits the server and closes the Twitch IRC socket. The bot is
non-functioning after this until [`start()`](#start) is called
again. The message is just a server quit message. It is not shown
anywhere.

**Note:** Timers are not closed by this command. It is up to the user to call
[`killTimers()`](#killTimers) on a full program exit.

**Input**  
`message`: Quit message string.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### msg

```python
bot.msg(message)
```

Sends a standard post to the chat. Will print the message to stdout to the level
the [print options](#Print-Options) allow.

**Input**  
`message`: String to post to chat.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### action

```python
bot.action(message)
```

Equivalent of using the /me command in chat.

**Input**  
`message`: String to post after /me.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### color

```python
bot.color(color)
```

Sets the color of the bot's username in chat. See the [Twitch commands](https://help.twitch.tv/customer/en/portal/articles/659095-chat-commands)
page for acceptable parameters.

**Input**  
`color`: Color string.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### ignore

```python
bot.ignore(username)
```

Ignores the defined username.

**Input**  
`username`: Username string.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### unignore

```python
bot.unignore(username)
```

Unignores the defined username.

**Input**  
`username`: Username string.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### timeout

```python
bot.timeout(username, time)
```

Times out the username, for specified time (in seconds).

**Input**  
`username`: Username string.  
`timer`: Time integer.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### purge

```python
bot.purge(username)
```

Times out the user for one second. It's only a call to [`bot.timeout(username, 1)`](#timeout),
but included since it's usually a common function in chat moderation.

**Input**  
`username`: Username string.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### ban

```python
bot.ban(username)
```

Bans the user.

**Input**  
`username`: Username string.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### unban

```python
bot.unban(username)
```

Unbans the user.

**Input**  
`username`: Username string.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### clear

```python
bot.clear()
```

Clears the chat.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### slowon

```python
bot.slowon(time)
```

Sets slow mode on in the chat.

**Input**  
`timer`: Time integer for required delay between posts.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### slowoff

```python
bot.slowoff()
```

Turns off slow mode.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### subson

```python
bot.subson()
```

Turns on subscriber-only mode.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### subsoff

```python
bot.subsoff()
```

Turns off subscriber-only mode.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### r9kon

```python
bot.r9kon()
```

Turns on R9K mode.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### r9koff

```python
bot.r9koff
```

Turns off R9K mode.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### emoteonlyon

```python
bot.emoteonlyon()
```

Turns on emote-only mode.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### emoteonlyoff

```python
bot.emoteonlyoff()
```

Turns off emote-only mode.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### getUserVar

```python
bot.getUserVar(varname)
```

Gets the value of the specified custom user variable.

**Input**  
`varname`: Variable name string.

**Return**  
Variable value (any type).

**Exceptions**  
`KeyError` if the variable does not exist.

---

#### setUserVar

```python
bot.setUserVar(varname, value)
```

Sets the value of the specified custom user variable.

**Input**  
`varname`: Variable name string.  
`value`: Any value to set the variable to.

**Return**  
Nothing.

**Exceptions**  
`KeyError` if the variable does not exist.

---

#### newUserVar

```python
bot.newUserVar(varname, value, vartype=None)
```

Creates a new variable. Outside the [config file](#Config-File), the variable type is optional,
as they are
in practice, untyped.

Variable names must start with a letter, and can only contain alphanumeric characters and
underscores.

**Input**  
`varname`: Variable name string.  
`value`: Variable value (any type).  
`vartype`: (optional) Variable type (any type).

**Return**  
Nothing.

**Exceptions**  
`KeyError` is variable name already exists.  
`ValueError` for an invalid variable name format.

---

#### delUserVar

```python
bot.delUserVar(varname)
```

Deletes the specified user variable.

**Input**  
`varname`: Variable name string.

**Return**  
Nothing.

**Exceptions**  
`KeyError` if variable does not exist.

---

#### getUserVarType

```python
bot.getUserVarType(varname)
```

Gets the specified user variable type. Can be of any type.

**Note:** Types only really matter for the [config file](#Config-File). This value is mostly irrelevant
and can be used for whatever in the program.

**Input**  
`varname`: Variable name string.

**Return**  
Variable type (any type).

**Exceptions**  
`KeyError` if variable does not exist.

---

#### setUserVarType

```python
bot.setUserVarType(varname, vartype)
```

Sets the specified user variable type. Can be of any type.

**Note:** Types only really matter for the config file. This value is mostly irrelevant
and can be used for whatever in the program.

**Input**  
`varname`: Variable name string.  
`vartype`: Vartype variable type (any type).

**Return**  
Variable type (any type).

**Exceptions**  
`KeyError` if variable does not exist.

---

#### initializeTimers

```python
bot.initializeTimers()
```

This sets up the TwitchBot timers. This must be called before any other timer interaction is
done.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### timersInitialized

```python
bot.timersInitialized()
```

Checks if timers are initialized. Simple.

**Input**  
Nothing.

**Return**  
`True` or `False`

**Exceptions**  
Nothing.

---

#### startTimers

```python
bot.startTimers()
```

Starts the timer loop. Depending on when this is called timers may already have passed their
trigger time. Try to call the function soon after any initial timer adds. Timers can still be
handled after the loop has started.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### timersStarted

```python
bot.timersStarted()
```

Checks if the timer loop has started.

**Input**  
Nothing.

**Return**  
`True` or `False`

**Exceptions**  
Nothing.

---

#### pauseTimers

```python
bot.pauseTimers()
```

Stops timers from executing, but does not kill the timers or the loop itself. Keep in mind
this only stops timers from executing. Single cycle timers can be removed while the loop is
paused and never execute.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### resumeTimers

```python
bot.resumeTimers()
```

Resumes the timer loop after a pause.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### killTimers

```python
bot.killTimers()
```

Kills the current initialized timer structure. All other timer commands are invalid until
[`initializeTimers()`](#initializeTimers) is called again, and all timers are removed.
Likely something to call before exiting the entire bot.

**Input**  
Nothing.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### addTimer

```python
bot.addTimer(ttype, delay, callback, args, rand=False, rrange=(), loop=False)
```

This adds a new timer to the bot. The timer will call the specified callback function
with the specified arguments. On the creation of a timer, a timer code is returned which is
needed for any timer specific functions.

For more, see [Timers](#Timers)

##### Timer options

Arg | Type | Behavior
--- | --- | ---
ttype | string | Either "sec", "min", or "hr". Defines what kind of interval the delay will be in.
delay | int | Number of time (specified in ttype) for timer to wait before executing. Ignored for random timers.
callback | function | The callback function the timer will call on execution.
args | anything | The argument object passed to the callback function. Make it a data structure for multiple parameters.
rand | boolean | Whether or not the timer will decide its delay at random. If `True`, ignores the constant delay.
rrange | tuple | 2 entry tuple of the upper and limit integer limits the random delay will be. Required if rand is `True`.
loop | boolean | If `True`, the timer will execute repeatedly. Otherwise, timer only runs once and is then removed.

**Input**  
Timer options.

**Return**  
Unique timer code (int).

**Exceptions**  
`ValueError` if `ttype` is invalid, if `rrange` is not of length 2, or if `rrange`'s values are not int.

---

#### removeTimer

```python
bot.removeTimer(code)
```

Removes an existing timer. If the timer does not exist the error is silently ignored.

**Input**  
`code`: Timer code.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### pauseTimer

```python
bot.pauseTimer(code)
```

Pauses an individual timer. Behavior type is the same as pausing all timers, but for only one.
If the timer does not exist the error is silently ignored.

**Input**  
`code`: Timer code.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### resumeTimer

```python
bot.resumeTimer(code)
```
Resumes an individual timer. Behavior type is the same as resuming all timers, but for only one.
If the timer does not exist the error is silently ignored.

**Input**  
`code`: Timer code.

**Return**  
Nothing.

**Exceptions**  
Nothing.

---

#### timerExists

```python
bot.timerExists(code)
```

Checks if a timer exists by code. Use this to counter the fact the
individual timer commands silently ignore errors.

**Input**  
`code`: Timer code.

**Return**  
`True` or `False`

**Exceptions**  
Nothing.
