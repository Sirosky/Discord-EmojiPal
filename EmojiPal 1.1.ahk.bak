;Discord EmojiPal by Sirosky
;v. 1.1 10/8/22

#Singleinstance, Force
#Include %A_ScriptDir%\external\export.ahk

#NoEnv  ; Recommended for performance and compatibility with future AutoHotkey releases.
SendMode Input  ; Recommended for new scripts due to its superior speed and reliability.
SetWorkingDir %A_ScriptDir%  ; Ensures a consistent starting directory.

;-----VARIABLE INITATION
global string := ""
global tracking := 0 ;Whether we're tracking input
FileRead, The_MemoryFile, external/emojis.json ; Load emojis
global emojis_raw := JSON.parse(The_MemoryFile)
global emojis := {}
emojis_sort(emojis_raw) ; Sort by alphabetical order
global ui_preview_string := ["","","","",""]
global ui_preview_select := 0 ; Current preview selected
global select_all := 0
global prime_exit := 0 ;Hit backspace twice when there's no text to exit app

;-----HOTKEY INITATION
IniRead, hot_activate, external/settings.ini, hotkeys, hot_activate
IniRead, hot_exit, external/settings.ini, hotkeys, hot_exit

Hotkey, IfWinActive, ahk_exe Discord.exe
Hotkey, %hot_exit%, EXITPROGRAM
Hotkey, %hot_activate%, ACTIVATE
Hotkey, ^%hot_activate%, HOTKEYIGNORE


GOSUB PREVIEWDOWNLOAD ; Download any missing previews on startup

keyevent(key) {

	if tracking = 1
	{

		;---Update search string as keys are inputted

		if ( key = "{BS}" ) ; Account for backspaces
		{
			global ui_preview_select = 0
			StringTrimRight, temp_string, string, 1
			global string = temp_string

			if (StrLen(string) = 0 and prime_exit = 1){

				prime_exit := 0
				GOSUB TRACKING ;If we hit backspace, and there's no text left, turn off tracking.
			}

			if (StrLen(string) = 0 and prime_exit = 0)  or select_all = 1 { ;When there's no text, don't exit immediately. Wait for user to hit backspace again
				prime_exit := 1
				string := ""
				select_all := 0
			}

			if StrLen(string) >= 2 { ; Fix for preview dimension issues
				GOSUB PREVIEWRESIZE

			}
			else { ;If we're below the minimum string length, don't show suggestions
				GOSUB PREVIEWRESET
				GOSUB UIUPDATE
			}
		}
		else ; Update primary string
		{
			if select_all = 1 ; If pressed Ctrl A, replace the string with the current key pressed
			{
				string := key
				select_all := 0
				prime_exit := 0
				ui_preview_select := 0
			}
			else  ; Normal input
			{
				string := string key
				prime_exit := 0
				ui_preview_select := 0
			}
		}


		;---Loop through available emojis and prepare hints

		if StrLen(string) >= 2 { ; Only suggest when there are two characters or more typed out
			GOSUB PREVIEWRESET

			array_count := 1
			for emoji in emojis

				if InStr(emoji, string) and array_count < 6 ; Prepare suggestions
				{
					ui_preview_string[array_count] := emoji
					array_count += 1
				}

		}

		if StrLen(ui_preview_string[ui_preview_select]) < 1{
					ui_preview_select := 0
					GOSUB PREVIEWSELECT
		}

		GOSUB UIUPDATE
		GOSUB PREVIEWUPDATE
	}
}

return

;---------- Handle events when user prepares to send an emoji and when user is ready to send the emoji

TRACKING:

tracking := !tracking ; Toggle tracking

if tracking = 0 ; Reset string, and print emoji
	{
		if ui_preview_select > 0 ; Select preview if needed
		{
			string := ui_preview_string[ui_preview_select]
		}

		Gui, Main:Destroy
		GOSUB PREVIEWRESET

		url := ""

		for key, value in emojis
			if string = %key%
			{
				url = %value%
			}

		; Send the actual emoji, or cancel
		Send ^a
		Send {Backspace}
		Send %url%
		Send {Enter}
		string := ""

	}
else ; Start tracking
	{
		gosub UISHOW
	}

return


;---------- Recreate the UI


UISHOW:

;--- Initiation
Gui, Main:Color, c272b38
Gui, Main:Font, c9cb1e4 s32 Q5 w600, Corbel
Gui, Main: Margin, 0, 0
Gui, Main:+LastFound +AlwaysOnTop +ToolWindow -DPIScale -Caption
WinSet, TransColor, c272b38 ;150
Gui, Main:Add, Picture, x0 y0 vui_bg, ui/bg.png
ui_preview_select := 0


Gui, Main:Add, Text, w600 x95 y-8 BackgroundTrans +Left vui_string, %string% ; Primary string being typed

;--- Hints
Gui, Main:Font, c7383a8 s28 Q5 w300, Corbel ; Change font size and weight
Gui, Main:Add, Picture, x50 y-20 BackgroundTrans vui_preview_select, ui/circle.png ; Make preview selector available, but hide it

for i, value in ui_preview_string ; Add preview images and text. i value starts at 1 in AHK loops.
{
	y_pos := (i * 46)
	y_pos_image := y_pos + 18
	Gui, Main:Add, Text, w600 x140 y%y_pos% BackgroundTrans +Left vui_preview_string%i%, %value%
	Gui, Main:Add, Picture, x92 y%y_pos_image% w-1 h36 BackgroundTrans vui_preview_image%i%, ui/blank.png
}

;--- Figure out where window should go, and show window.
SysGet, Mon, Monitor

x_pos := MonRight/2 - 300
y_pos := MonBottom/2

;msgbox, % x_pos
Gui, Main:Show, NA x%x_pos% y%y_pos% w600 h300, EmojiPal


return

;---------- Update the hints and preview images. Also clears previews.

UIUPDATE:

GuiControl, Main:, ui_string, %string% ; Update primary string

if StrLen(string) >= 2{ ; Update all preview text
	for i, value in ui_preview_string {
		GuiControl, Main:, ui_preview_string%i%, %value%
	}
}
else { ; If called upon with insufficient string length, clear all previews

	for i, value in ui_preview_string {
		GuiControl, Main:, ui_preview_string%i%,
		GuiControl, Main:, ui_preview_image%i% , ui/blank.png
		ui_preview_select := 0
	}

}
return

PREVIEWUPDATE: ; Using a separate subroutine to update images for optimization

if StrLen(string) >= 2{
	for i, value in ui_preview_string {
		preview_extension := ".png"
		image := A_ScriptDir . "\previews\" . value . preview_extension
		;Msgbox, % image

		GuiControl, Main:, ui_preview_image%i% , %image%
	}
}
return

;---------- Only called to fix dimension issues with the preview images. Workaround because AHK is retarded and doesn't take w-1 in GuiControl.

PREVIEWRESIZE:

if StrLen(string) >= 2{
	for i, value in ui_preview_string {
		GuiControl, Main:Move, ui_preview_image%i% , w36 h36
	}
}

return

;---------- Selects previewed text for input using arrow keys.

PREVIEWSELECT:

if !ui_preview_select = 0 {
	;GuiControl, Main:, ui_string, %string%

	y_pos := (ui_preview_select * 46) + 28

	GuiControl, Main:Move, ui_preview_select, x72 y%y_pos% ;Move the selector
}
else {
	GuiControl, Main:Move, ui_preview_select, x72 y-20 ;Move the selector back offscreen
}

return

;---------- Resets previews.

PREVIEWRESET:

ui_preview_select := 0
ui_preview_string[1] := ""
ui_preview_string[2] := ""
ui_preview_string[3] := ""
ui_preview_string[4] := ""
ui_preview_string[5] := ""

return

;---------- Downloads previews of emojis. GIFs are converted to PNG since AHK can't display animated GIFs anyway...

PREVIEWDOWNLOAD:

for key, value in emojis {

	preview_local := "\previews\" . key . ".*"
	preview_extension := ".png"
	preview_download := "\previews\" . key . preview_extension


	if !FileExist(A_Scriptdir . preview_local) {
		;Msgbox % key
		UrlDownloadToFile, %value%, %A_ScriptDir%%preview_download%
	}

}

return

emojis_sort(array) {

	temp := {}

	for k, v in array {
		temp.Push(k)
		;Msgbox, % k
	}
	sortArray(temp)


	for k, v in temp {
		; v = name of the emoji, k is the number used in the temp array

		;emojis.Push({(v): emojis_raw[v]}) ;This isn't quite right
		emojis[v] := emojis_raw[v]

	}
	;GOSUB SAVETEST ;Testing to make sure the associative array sorted correctly
}

sortArray(arr,options="") {	; specify only "Flip" in the options to reverse otherwise unordered array items

	if	!IsObject(arr)
		return	0
	new :=	[]
	if	(options="Flip") {
		While	(i :=	arr.MaxIndex()-A_Index+1)
			new.Insert(arr[i])
		return	new
	}
	For each, item in arr
		list .=	item "`n"
	list :=	Trim(list,"`n")
	Sort, list, %options%
	Loop, parse, list, `n, `r
		new.Insert(A_LoopField)
	return	new

}

;^!D::
;GOSUB EXITPROGRAM

EXITPROGRAM:
ExitApp
;----------  Hotkey stuff

#IfWinActive ahk_exe Discord.exe

ACTIVATE:
GOSUB TRACKING
return


HOTKEYIGNORE: ; Ignore the app and type the key that is assigned as the activation key
Send {%hot_activate% down}
return

Enter::

;--- Allow using Enter key to send autofills
if (tracking = 1) and (StrLen(ui_preview_string[1]) > 2) and (ui_preview_select > 0) {
	GOSUB TRACKING
	Exit
}

if (tracking = 1) {

	match := 0

	for key, value in emojis
		if string = %key%
		{
			match := 1

		}

	if match = 1
		GOSUB TRACKING

}


Send {Enter}


return

;----------  Intercept up and down keys while in Discord


Up::

if StrLen(ui_preview_string[1]) < 1  ; Let the input through
	Send {Up}

else ; Update preview selection
{
	if ui_preview_select > 0 ;Move up
	{
		ui_preview_select -= 1
		GOSUB PREVIEWSELECT
	}
	if ui_preview_select <= 0 ;Cycle to the bottom if already at top
	{

		for i in ui_preview_string ;Find the last preview to cycle to.
		{

			if StrLen(ui_preview_string[i]) > 1
			{
				ui_preview_select := i
			}
		}


		GOSUB PREVIEWSELECT
	}
}
return


Down::

if StrLen(ui_preview_string[1]) < 1  ; Let the input through
	Send {Down}
else { ; Update preview selection
	preview_check := ui_preview_select + 1

	if StrLen(ui_preview_string[preview_check]) >= 1 { ; Make sure there's actually something there
		ui_preview_select += 1
		GOSUB PREVIEWSELECT
	}
	if ui_preview_select = 5 ;Cycle back to the top if already at the bottom
	{
		ui_preview_select = 1
		GOSUB PREVIEWSELECT
	}
}
return

;----------  Escape key hides the app while only in Discord

Esc::

if tracking = 1
	tracking = 0
	string := ""
	Gui, Main:Destroy
	GOSUB PREVIEWRESET

return

;---------- Support Ctrl + A

^A::

if tracking = 1
	select_all := 1
else
	Send, ^a

return





;------ https://github.com/nbirnel/keylogger/blob/master/keylogger.ahk

#If tracking = 1
{
	a::keyevent("a")
	b::keyevent("b")
	c::keyevent("c")
	d::keyevent("d")
	e::keyevent("e")
	f::keyevent("f")
	g::keyevent("g")
	h::keyevent("h")
	i::keyevent("i")
	j::keyevent("j")
	k::keyevent("k")
	l::keyevent("l")
	m::keyevent("m")
	n::keyevent("n")
	o::keyevent("o")
	p::keyevent("p")
	q::keyevent("q")
	r::keyevent("r")
	s::keyevent("s")
	t::keyevent("t")
	u::keyevent("u")
	v::keyevent("v")
	w::keyevent("w")
	x::keyevent("x")
	y::keyevent("y")
	z::keyevent("z")
	_::keyevent("_")
	0::keyevent("0")
	1::keyevent("1")
	2::keyevent("2")
	3::keyevent("3")
	4::keyevent("4")
	5::keyevent("6")
	6::keyevent("6")
	7::keyevent("7")
	8::keyevent("8")
	9::keyevent("9")
	SC00E::keyevent("{BS}")
	$BACKSPACE::keyevent("{BS}")
	Space::keyevent(A_Space)
}


; ------ TEST SAVING. Don't need this anymore.

SAVETEST:
save := JSON.stringify(emojis)
FileDelete, external/test.json
FileAppend, %save%, external/test.json
return

;---------- Exit with Ctrl + Alt + D (Default)



