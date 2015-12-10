

# Introduction #

The MPlayer commands and properties found in http://www.mplayerhq.hu/DOCS/tech/slave.txt are mapped to methods and properties, respectively.

In actuality, only the `quit` command is hard-coded in `Player` (as the `quit()` method). All other methods such as `loadfile()`, `pause()`, etc. and properties such as `time_pos`, `fullscreen`, etc. are automatically generated upon importing the `mplayer` module. In the case that the MPlayer binary is located in a non-standard location, introspection can be manually initiated as follows:

```
from mplayer import Player

# First, specify the correct path to the MPlayer executable
Player.exec_path = '/actual/path/to/the/mplayer-bin'

# Then, manually call the introspect() class method
Player.introspect()
```

**Note that introspection also applies to all subclasses of `Player`.**

## Introspection Rules ##
Because of the dynamic nature of the wrapper, the exposed<sup>1</sup> or hidden<sup>2</sup> methods and properties (attributes, collectively) depend on the version of MPlayer introspected. However, the list of attributes is predictable because introspection follows the _rules_ listed below:

  1. Expose ALL properties
  1. Hide ALL `get_*` commands (e.g. `get_file_name`) `[`because properties already cover the functionality of these commands`]`
  1. Hide ALL `*_property` commands (e.g. `set_property`) `[`because these commands are used internally`]`

<sup>1</sup>exposed in the `Player` API as a method or property; can be accessed normally

<sup>2</sup>not available in the `Player` API but can be accessed using some of the _private_ methods of `Player` (but is not recommended)

# Details #

## Types ##

MPlayer types are automatically converted to a corresponding Python type. The type map is shown in the following table:

| **MPlayer Type** | **Python Type** |
|:-----------------|:----------------|
| `Flag`           | `bool`          |
| `Integer`        | `int`           |
| `Position`       | `int`           |
| `Float`          | `float`         |
| `Time`           | `float`         |
| `String`         | `str`           |
| `String list`<sup>*</sup> | `dict`          |

**NOTE:**
<sup>*</sup>`String list` is actually a list of strings separated by a comma. It is currently mapped to `dict` because the only `String list` type is the `metadata` property.

## Properties ##

MPlayer properties can be listed using: `mplayer -list-properties`

All MPlayer properties are implemented as Python properties (new-style classes). This means that the functionality of the `get_property` and `set_property` commands are implemented using property _getters_ and _setters_, respectively. Since Python properties do not inherently support _stepping_ `[`up or down`]` a property, the `Step` class is used, together with standard [property access](Introspection#Property_Access.md), to implement the `step_property` command.

The following table lists the properties that were renamed:

| **Property** | **Status** | **Remarks** |
|:-------------|:-----------|:------------|
| `pause`      | renamed    | Renamed to `paused` to avoid conflict with the `pause()` method |

## Commands ##

MPlayer commands can be listed using: `mplayer -input cmdlist`

Most MPlayer commands are implemented as Python methods except the `get_*` commands (e.g. `get_file_name`) and all other commands which duplicate the functionality of a corresponding MPlayer property. In other words, MPlayer properties take precedence over commands. That is, for instance, the `audio_delay` command will not be implemented in favor of the `audio_delay` property.

The following table lists some of the commands that are not generated (note that this will vary depending on the version of MPlayer):

| **Command** | **Status** | **Remarks** |
|:------------|:-----------|:------------|
| `get_audio_bitrate` | excluded   | Use `audio_bitrate` property instead |
| `get_audio_codec` | excluded   | Use `audio_codec` property instead |
| `get_audio_samples` | excluded   | Use `samplerate` and `channels` properties instead |
| `get_file_name` | excluded   | Use `filename` property instead |
| `get_meta_album` | excluded   | Use `metadata['Album']` instead |
| `get_meta_artist` | excluded   | Use `metadata['Artist']` instead |
| `get_meta_comment` | excluded   | Use `metadata['Comment']` instead |
| `get_meta_genre` | excluded   | Use `metadata['Genre']` instead |
| `get_meta_title` | excluded   | Use `metadata['Title']` instead |
| `get_meta_track` | excluded   | Use `metadata['Track Number']` instead |
| `get_meta_year` | excluded   | Use `metadata['Year']` instead |
| `get_percent_pos` | excluded   | Use `percent_pos` property instead |
| `get_sub_visibility` | excluded   | Use `sub_visibility` property instead |
| `get_time_length` | excluded   | Use `length` property instead |
| `get_time_pos` | excluded   | Use `time_pos` property instead |
| `get_vo_fullscreen` | excluded   | Use `fullscreen` property instead |
| `get_video_bitrate` | excluded   | Use `video_bitrate` property instead |
| `get_video_codec` | excluded   | Use `video_codec` property instead |
| `get_video_resolution` | excluded   | Use `width` and `height` properties instead |
| `get_property` | excluded   | Internally used |
| `set_property` | excluded   | Internally used |
| `step_property` | excluded   | Internally used, see usage of the `Step` class [below](Introspection#Property_Access.md) |

# Usage #

## Methods ##

MPlayer commands are implemented as Python methods. As such, they can be used like so:
```
p = Player()

# Use the loadfile command
p.loadfile('/path/to/file.mkv')

# Use the pause command
p.pause()
```

## Property Access ##

To implement the `step_property` command, the `Step` class, together with standard property access, is used.
```
Step(value=0, direction=0)
```

MPlayer properties are implemented as Python properties. As such, they can be used like so (notice the automatic type conversion):

```
from mplayer import Player, Step

p = Player()

p.loadfile('/path/to/file.mkv')

# Get the value of the 'fullscreen' property
# Equivalent to 'get_property fullscreen'
p.fullscreen # returns a bool, True or False

# Get the value of the 'time_pos' property
# Equivalent to 'get_property time_pos'
p.time_pos # returns a float

# Set to fullscreen
# Equivalent to 'set_property fullscreen 1'
p.fullscreen = True

# Step fullscreen property. Stepping a bool-type property is the same as toggling it.
# Equivalent to 'step_property fullscreen'
p.fullscreen = Step()

# Seek to start of file
# Equivalent to 'set_property time_pos 0'
p.time_pos = 0

# Seek +30 seconds
# Equivalent to 'get_property time_pos', then 'set_property time_pos <current + 30>'
p.time_pos += 30

# Seek +30 seconds (faster than the one above)
# Equivalent to 'step_property time_pos 30'
p.time_pos = Step(30)

# Seek -10 seconds
# Equivalent to 'step_property time_pos -10'
p.time_pos = Step(-10)

# Step up using the default value
# Equivalent to 'step_property time_pos'
p.time_pos = Step()

# Step down using the default value
# Equivalent to 'step_property time_pos 0 -1'
p.time_pos = Step(direction=-1)

# Seek -10 seconds in the negative direction (same as seek +10 seconds)
# Equivalent to 'step_property time_pos -10 -1'
p.time_pos = Step(-10, -1)
```