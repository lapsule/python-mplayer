# Synopsis #

```
Player(args=(), stdout=PIPE, stderr=None, autospawn=True)
```

An out-of-process wrapper for MPlayer. It provides the basic Pythonic interface for sending commands and receiving responses to and from MPlayer. Take note that MPlayer is always started in 'slave', 'idle', and 'quiet' modes.

  * `args` - additional arguments to be passed to MPlayer
  * `stdout` - handle to MPlayer's stdout (`mplayer.PIPE`, `None`, or any other open `file`)
  * `stderr` - handle to MPlayer's stderr (`mplayer.PIPE`, `mplayer.STDOUT`, `None`, or any other open `file`)
  * `autospawn` - spawn the underlying MPlayer process upon instantiation

## Class Attributes ##

### `exec_path` ###
path to the MPlayer executable (default: `'mplayer'`)

**NOTE:** In 0.6.0, this was named as **`path`**

### `cmd_prefix` ###
prefix used for MPlayer commands (default: `CmdPrefix.PAUSING_KEEP_FORCE`).

This is particularly useful for changing the MPlayer behavior when accessing properties.

**NOTE:** In 0.6.0, this was named as **`command_prefix`**

**(NOTE: 0.6.0 release ONLY)** For generated methods, you can specify the prefix to be used like so:
```
from mplayer import Player, CommandPrefix

p = Player()
...
# Use 'pausing_toggle' JUST for this call
p.get_meta_artist(prefix=CommandPrefix.PAUSING_TOGGLE)
```

### `version` ###
This is the version of the introspected MPlayer executable. Its default value is `None`.

**NOTE:** Not in 0.6.0; only available in latest Hg clone.
# Sample Usage #
**NOTE:** In 0.6.0, `CmdPrefix` is named as `CommandPrefix`

```
from mplayer import Player, CmdPrefix

# Set default prefix for all Player instances
Player.cmd_prefix = CmdPrefix.PAUSING_KEEP

# Since autospawn is True by default, no need to call player.spawn() manually
player = Player()

# Play a file
player.loadfile('/path/to/file.mkv')

# Pause playback
player.pause()

# Get title from metadata
metadata = player.metadata or {}
print metadata.get('Title', '')

# Print the filename
print player.filename

# Seek +5 seconds
player.time_pos += 5

# Set to fullscreen
player.fullscreen = True

# Terminate MPlayer
player.quit()
```