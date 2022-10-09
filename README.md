![](https://i.imgur.com/M09bswI.png)

* * *

EmojiPal is a completely free and open source application to send all your favorite emojis in Discord without using Nitro made with [AutoHotKey](https://www.autohotkey.com/). Please note that as AHK only supports Windows, this is a Windows-only application. Some pre-loaded emojis may not be appropriate for children. User discretion advised.


https://user-images.githubusercontent.com/2752448/194776880-c567aecb-5d45-4aa1-a409-32b0fdaee598.mp4


## Features


- 100% free and open source, forever.
- Seamless integration with Discord.
- Over 150 emojis, stickers and GIFs preloaded...
- ...and easily add more of your own!
- Autosuggest emojis and complete emoji prompts.
- Ultra lightweight-- the program itself is less than 1.5 MB. While running, it takes 1.5 - 4 MB of RAM.
- No injection into the Discord client-- EmojiPal does not access your Discord token.

## How to use


1.  Grab the latest release [here](https://github.com/Sirosky/Discord-EmojiPal/releases/new).
2.  Extract the app to where you plan to keep it.
3.  Run EmojiPal.exe.
4.  \[**Optional -** **Recommended**\] To have EmojiPal boot on Windows startup, right click EmojiPal.exe and create a shortcut. Paste the shortcut into the Windows startup folder (accessed by pressing Win + R and typing `shell:startup`).
5.  Switch back to Discord, and press ";" (default hotkey) to begin.
6.  \[Optional\] Check out the `previews` folder for a list of each emoji and their associated prompt. The file name is the exact emoji prompt. I would display them here, but there's far too many.

## Controls


*Note that the controls are only active when Discord is the window you have in focus. This is to prevent it from interfering with hotkeys in your other programs.*

- **;** \- Semicolon is the default hotkey for activating the app. Pressing it after typing out a prompt will send the prompt if it is valid, or hide the app if the prompt is invalid.
- **Up** and **down arrow** keys - When typing out a prompt, use the up and down arrow keys to select an autosuggested emoji.
- **Enter** -  Sends a completed emoji. Will also send emojis when there is an autosuggested emoji. Unlike semicolon, will not hide the app if a prompt is invalid.
- **Esc** \- Hides the app.
- **Ctrl** \+ **Alt** \+ **D** \- Exits the app entirely. This is the default hotkey.
- **Ctrl** \+ **A** \- Selects the entire emoji prompt. Pressing backspace deletes the entire prompt. Pressing a letter replaces the prompt with said letter.
- **Ctrl**\+ Activation Hotkey - Sends the hotkey being used to activate the app, rather than activating the app. For example, sending Ctrl + ; will send `;` instead of activating the app. This also means you cannot use Ctrl as part of the keyboard combination to activate the app.

## Changing controls


In the `external` folder, locate and open `settings.ini`. There, you can change the keys to activate the app and to exit out of the app. AutoHotKey hotkey prefixes can be located [here](https://www.autohotkey.com/docs/KeyList.htm), if you want to use keyboard combinations. However, using : as the activation hotkey is not recommended, as it'll lock you out of sending Discord emojis through hotkeys.

## Adding and removing emojis


Adding and removing emojis is simple.

1.  In the same `external` folder, locate and open `emojis.json` with your text editor of choice.
2.  Add new emojis by adding the following syntax (keep the quotes) `,"emoji_name_here":"direct link to image"` to the end of the JSON file (but before the last curly quote).
    1.  An example is `,"soymald":"https://cdn.frankerfacez.com/emoticon/652501/2"`
    2.  Be sure to use direct links to images, otherwise there will be an embed fail.
    3.  You may also upload emojis to your image hosting site of choice and use the direct link from that site.
3.  To remove an emoji, find the emoji name using Ctrl + F and replace the full entry, e.g., delete this `,"emoji_to_be_deleted":"link"`
4.  Save the JSON file, then restart the app.
5.  When adding new emojis, at startup, the app should automatically download a preview into the `previews` folder.

While we're at it, here are a few websites I pulled emojis from.

- [FrankerFaceZ](https://www.frankerfacez.com/emoticons/)
- [Discadia](https://discadia.com/emojis/)
- [Emotes.io](/C:/Program%20Files/Joplin/resources/app.asar/emotes.io/ "emotes.io/")

## Under the hood

EmojiPal sends emojis by taking advantage of Discord embeds-- when you enter an emoji prompt, it'll send a direct link to the emoji image. This is essentially how all of the "free Nitro" emoji-sending scripts work. A limitation is that you cannot have text and a picture emoji on the same line, unlike normal Discord emojis. Some channels may also block linking, which prevents EmojiPal and other "free Nitro"-style scripts from sending Emojis. But generally, this isn't too common.
