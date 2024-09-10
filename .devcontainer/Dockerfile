FROM debian:trixie-backports

ARG USERNAME=doc
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
     && apt-get -y install --no-install-recommends \ 
     ca-certificates sudo gcc-14 g++-14 git zsh rootlesskit libcap2-bin less nano

RUN cp /usr/bin/cpp-14 /usr/bin/cpp \
    && cp /usr/bin/gcc-14 /usr/bin/gcc \
    && cp /usr/bin/g++-14 /usr/bin/g++ \
    && cp /usr/bin/gcc-ar-14 /usr/bin/gcc-ar  \
    && cp /usr/bin/gcc-nm-14 /usr/bin/gcc-nm  \
    && cp /usr/bin/gcc-ranlib-14 /usr/bin/gcc-ranlib \
    && cp /usr/bin/gcov-14 /usr/bin/gcov \
    && cp /usr/bin/gcov-dump-14 /usr/bin/gcov-dump \
    && cp /usr/bin/gcov-tool-14 /usr/bin/gcov-tool \
    && cp /usr/bin/lto-dump-14 /usr/bin/lto-dump

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME -s /usr/bin/zsh \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

#WORKDIR /home/doc
COPY <<"EOF" /home/$USERNAME/.zshrc
export LSCOLORS="ExfxcxdxbxEgEdabagacad"
export LS_COLORS="di=1;34:ln=35:so=32:pi=33:ex=31:bd=1;34;46:cd=1;34;43:su=30;41:sg=30;46:tw=30;42:ow=30;43"
alias ls='ls --color'

autoload -Uz compinit && compinit

# Remove superfluous blanks from each command line being added to the history
# list
setopt histreduceblanks
# Remove command lines from the history list when the first character on the
# line is a space, or when one of the expanded aliases contains a leading space
setopt histignorespace
setopt sharehistory
# Do not enter command lines into the history list if they are duplicates of the
# previous event.
setopt histignorealldups

# See: http://zsh.sourceforge.net/Intro/intro_2.html
setopt extendedglob
# Do not require a leading '.' in a filename to be matched explicitly
setopt globdots

# Switching directories for lazy people
setopt autocd

# If the argument to a cd command (or an implied cd with the AUTO_CD option set)
# is not a directory, and does not begin with a slash, try to expand the
# expression as if it were preceded by a '~'
setopt cdablevars
# Try to make the completion list smaller (occupying less lines) by printing
# the matches in columns with different widths
setopt listpacked
# Don't show types in completion lists
setopt nolisttypes
# If a completion is performed with the cursor within a word, and a full
# completion is inserted, the cursor is moved to the end of the word
setopt alwaystoend

# https://github.com/robbyrussell/oh-my-zsh/issues/449
setopt no_nomatch
# Disable annoying confirm
setopt rmstarsilent

git_branch() {
  branch=$(git symbolic-ref --short HEAD 2>/dev/null)
  [[ -n $branch ]] && echo "%F{2}[$branch]%f"
}

setopt prompt_subst
PROMPT='%F{10}%n@%m%f:%B%F{12}%3~%f%b$(git_branch) %# '

# Type start of cmd and use up/down arrow to complete it from history

# You need to make sure that ^[[B and ^[[A are the keyboard sequences for Down and Up arrow keys
# You can test that by pressing CTRL-V and then Down or Up arrow, this displays the keyboard sequence
# NOTE: For me it is not ^[[B anymore..

bindkey "^[OA" history-beginning-search-backward
bindkey "^[OB" history-beginning-search-forward

# FN + < > arrows
bindkey "^[[H" beginning-of-line
bindkey "^[[F" end-of-line

EOF

USER $USERNAME
