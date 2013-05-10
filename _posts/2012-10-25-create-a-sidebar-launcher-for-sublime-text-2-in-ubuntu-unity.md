---
layout: post
title: Create a Sidebar launcher for Sublime Text 2 in Ubuntu Unity 
permalink: /2012/10/create-a-sidebar-launcher-for-sublime-text-2-in-ubuntu-unity/index.html
tags: [ ubuntu, howto ]
published: true
---

Using the example in <a href="http://www.technoreply.com/how-to-install-sublime-text-2-on-ubuntu-12-04-unity/">this post</a>, I created a launcher icon for Sublime Text 2 in Ubuntu 12.10.

Here is how:

    sudo nano -w  /usr/share/applications/sublime.desktop</pre>

Then paste in the following (<span style="text-decoration: underline;"><strong>make sure to update my file paths with your file paths</strong></span>):

	[Desktop Entry]
	Version=1.0
	Name=Sublime Text 2
	# Only KDE 4 seems to use GenericName, so we reuse the KDE strings.
	# From Ubuntu's language-pack-kde-XX-base packages, version 9.04-20090413.
	GenericName=Text Editor

	Exec=/home/jon/Programs/SublimeText2/sublime_text
	Terminal=false
	Icon=/home/jon/Programs/SublimeText2/Icon/48x48/sublime_text.png
	Type=Application
	Categories=TextEditor;IDE;Development
	X-Ayatana-Desktop-Shortcuts=NewWindow

	[NewWindow Shortcut Group]
	Name=New Window
	Exec=/home/jon/Programs/SublimeText2/sublime_text
	TargetEnvironment=Unity
	
Then click the Ubuntu button on the top of the sidebar. Type "Sublime" and notice that SublimeText2 appears as a launcher. Open it and right click to lock to your launcher bar.