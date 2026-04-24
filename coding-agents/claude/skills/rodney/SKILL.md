---
name: rodney
description: This skill uses the rodney CLI tool for browser automation. Use this for all browser based testing and automation.
---

# Rodney

## Instructions

[Rodney](https://github.com/simonw/rodney) provides Chrome automation from the command line.

It is a Go CLI tool that drives a persistent headless Chrome instance using the rod browser automation library. Each command connects to the same long-running Chrome process, making it easy to script multi-step browser interactions from shell scripts or interactive use.

It is already installed on this machine.

Run `rodney --help`, for command line options.

See the [README](https://github.com/simonw/rodney/blob/main/README.md) for instruction on use.

## Examples

Here is a shell script example of a single rodney session.

```bash
# Wait for page to load and extract data
rodney start --show
rodney open https://example.com
rodney waitstable
title=$(rodney title)
echo "Page: $title"

# Conditional logic based on element presence
if rodney exists ".error-message"; then
    rodney text ".error-message"
fi

# Loop through pages
for url in page1 page2 page3; do
    rodney open "https://example.com/$url"
    rodney waitstable
    rodney screenshot "${url}.png"
done

rodney stop
```