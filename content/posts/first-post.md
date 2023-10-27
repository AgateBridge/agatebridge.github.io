+++
title = 'Documenting Your Session the Tmux Way'
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

```#
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-logging'

# Other examples:
# set -g @plugin 'github_username/plugin_name'
# set -g @plugin 'github_username/plugin_name#branch'
# set -g @plugin 'git@github.com:user/plugin'
# set -g @plugin 'git@bitbucket.com:user/plugin'

# Set logging directory
set -g @logging-path $PWD


# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

## Tmux-Logging Modificaitons:

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

Full credit to these guides on both tmux session and window automation, and the "send-keys" options:  
https://how-to.dev/how-to-create-tmux-session-with-a-script

https://tao-of-tmux.readthedocs.io/en/latest/manuscript/10-scripting.html

/path/to/your/capture\_tmux.sh

```
#!/bin/bash

tmux new-session -d -s $session

window=0
tmux rename-window -t $session:$window 'main'
tmux send-keys -t $session:$window '/home/$USER/.tmux/plugins/tmux-logging/scripts/toggle_logging.sh' Enter

tmux attach-session -t $session

```

## Alias command to startup Tmux easily:

~/.{bash/zsh/fish}.rc

`alias capture=/path/to/your/capture_tmux_session.sh'`

Don't forget to chmod +x

Final result:

{Insert Video Demonstration here}
