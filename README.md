# setbg
Set the background color of the current macOS Terminal window.

## Usage
```zsh
$ setbg indigo
$ setbg "#4B0082"
```

## Installation
Drop this into your `.zshrc` or `.bashrc` file and restart your terminal.

```zsh
# Set the background color of the current terminal window
setbg() {
  local color
  local applescript

  # Define color dictionary (extend this as needed)
  declare -A colors
  colors=(
    ["black"]="0,0,0"
    ["blue"]="0,0,65535"
    ["red"]="65535,0,0"
    ["green"]="0,42685,0"
    ["orange"]="65535,12768,0"
    ["rouge"]="65535,0,21845"
    ["slate"]="13107,32896,45941"
    ["gray"]="21845,21845,21845"
    ["indigo"]="21845,0,65535"
  )

  # Helper function to print help message
  print_help() {
    echo "Usage: setbg COLOR_NAME | HEX_VALUE"
    echo "Change the background color of the Terminal."
    echo ""
    echo "Available color names:"
    if [ "$ZSH_VERSION" ]; then
      for name in "${(k)colors[@]}"; do
        echo "  $name"
      done
    else
      for name in "${!colors[@]}"; do
        echo "  $name"
      done
    fi
  }

  # Check for help flag
  if [[ $1 == "-h" || $1 == "--help" ]]; then
    print_help
    return 0
  fi

  # Check if input is a color name
  if [[ -n ${colors[$1]} ]]; then
    color=${colors[$1]}
  # Check if input is a hex value
  elif [[ $1 =~ ^#?[0-9A-Fa-f]{6}$ ]]; then
    hex=${1#"#"}  # Remove the '#' if present
    color="$(printf "%d,%d,%d" $((0x${hex:0:2} * 65535 / 255)) $((0x${hex:2:2} * 65535 / 255)) $((0x${hex:4:2} * 65535 / 255)))"
  else
    echo "Error: Invalid color name or hex value.\n" >&2
    print_help
    return 1
  fi

  applescript="
  tell application \"Terminal\"
    set currentSettings to current settings of front window
    set background color of currentSettings to {${color}}
  end tell
  "

  osascript -e "$applescript"
}

# Add this to your .bashrc or .zshrc file
alias setbg=setbg

```

----


Co-authored with ChatGPT.
