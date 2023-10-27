+++
title = 'Documenting Your Terminal Session: The Tmux Way'
date = 2023-10-26T19:40:42-05:00
draft = false
+++

![Example Image](/img/tmux.png#center)

# Premise:

Why would someone want to record their terminal session? Perhaps you're doing a profesisonal pentest, or are just forgetful
like I am when it comes to HackTheBox, recording solutions, or even planning to do a writeup in the future.

No matter what your reasoning, the tmux-logging pluggin is here to save you some time, if you're willing to put some back into it. Below are general directions for a *.nix system.

### Requirements:

- Tmux
- Tmux plugin manager (https://github.com/tmux-plugins/tpm)
- Tmux-logging (https://github.com/tmux-plugins/tmux-logging/tree/master)

## Tmux config:

Here you will define the plugins to be used, and what path your logs will save to via a variable. That variable is defined later.

```#
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-logging'


# Set logging directory
set -g @logging-path $PWD


# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

## Tmux-Logging Modificaitons:

Tmux-logging is made up of .sh scripts, here you can adjust as you like as there is no built in auto start functionality.

~/.tmux/plugins/tmux-logging/scripts/variables.sh

Rename your "default\_logging\_filename" to adopt the name of the folder/project you are currently in.

```#
default_logging_path="$HOME"
logging_path=$(tmux show-option -gqv "@logging-path")
logging_path=${logging_path:-$default_logging_path}

** default_logging_filename="tmux-$(basename $PWD).log" **
logging_filename=$(tmux show-option -gqv "@logging-filename")
logging_filename=${logging_filename:-$default_logging_filename}
```

## Script to automatically start tmux with logging:

This is my go to script when I start a HTB lab. This is later via a bash alias.

Full credit to these guides on both tmux session and window automation, and the "send-keys" options:  
https://how-to.dev/how-to-create-tmux-session-with-a-script

https://tao-of-tmux.readthedocs.io/en/latest/manuscript/10-scripting.html

Note: This is just the path to your script:

/path/to/your/capture_tmux.sh

```
#!/bin/bash

tmux new-session -d -s $session

window=0
tmux rename-window -t $session:$window 'main'
tmux send-keys -t $session:$window '/home/$USER/.tmux/plugins/tmux-logging/scripts/toggle_logging.sh' Enter

tmux attach-session -t $session

```

## Alias command to startup Tmux easily:

Here is the alias mentioned above.

~/.{bash/zsh/fish}.rc

`alias capture=/path/to/your/capture_tmux_session.sh'`

Don't forget to chmod +x!

## Result

Once "capture" is typed into your terminal of choice a tmux session is created, with the name 'main', and the "toggle_logging.sh" script is started. Once ran you attach to the session. Seamless as can be on the user's end, feel free to shoot me a message if you have any questions about it, enjoy!

