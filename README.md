# tbkeys

`tbkeys` is an add-on for Thunderbird that uses [Mousetrap](https://craig.is/killing/mice) to bind key sequences to custom commands.

## Install

* Download the tbkeys.xpi file from one of the releases listed on the [GitHub releases page](https://github.com/willsALMANJ/tbkeys/releases).
* Open the Add-ons Manager in Thunderbird (Tools->Add-ons).
* Click on the gear icon in the upper right and choose "Install Add-on From File..." and then select the downloaded tbkeys.xpi file.
* The add-on will self-update from the GitHub releases page when future updates are released.

## Default keybindings

The default keybindings are modeled on GMail's keybindings.

| Key | Function |
| --- | -------- |
|  c  | Compose new message  |
|  r  | Reply |
|  a  | Reply all |
|  f  | Forward |
|  #  | Delete |
|  u  | Refresh mail. If a message tab is open, close it. |
|  j  | Next message |
|  k  | Previous message |
|  o  | Open message |
|  x  | Archive message |

## Customizing keybindings

To customize keybindings, modify the "Key bindings" entry in the add-on's preferences pane which can be accessed from the add-on's entry in the Add-ons Manager ("Add-ons" in the Thunderbird menu).
The "Key bindings" entry should be a JSON object mapping keybindings (with Mousetrap syntax as described [here](https://craig.is/killing/mice) to javascript code to execute (see [here](https://hg.mozilla.org/comm-central/file/tip/mail/base/content/mainCommandSet.inc.xhtml) for cmd name references).
This [old wiki page about Keyconfig](http://kb.mozillazine.org/Keyconfig_extension:_Thunderbird) also has some commands that are still valid.
The Developer Toolbox (Tools->Developer Tools->Developer Toolbox in the menu) can be useful for poking around at the UI to find the name of an element to call a function on.
The preferences page will not allow invalid JSON to be submitted, but it does not sanity check the keybindings otherwise.

### Command syntax

A few different styles of commands can be specified for keybindings.
They are:

* **Simple commands**: These follow the format `cmd:<command_name>` where `<command_name>` is a command that Thunderbird can execute with `goDoCommand()`.
Most command names can be found in [the main command set file](https://hg.mozilla.org/comm-central/file/tip/mail/base/content/mainCommandSet.inc.xhtml) of the Thunderbird source code.
* **Simple function calls**: These follow the format `func:<func_name>` where `<func_name>` is a function defined on the Thunderbird window object.
That function is called without any arguments.
* **Custom function calls**: The follow the format `tbkeys:<func_name>` where `<func_name>` is the name of a custom function written in tbkeys.
Currently, the only available custom function is `closeMessageAndRefresh` which closes the open tab if it is not the first tab and then refreshes all accounts.
This behavior mimics the behavior of the GMail keybinding `u`.
* **Unset binding**: These entries simply contain the text `unset`.
When an `unset` keybinding is triggered, nothing happens.
This can be useful unbinding built-in Thunderbird key bindings which you do not wish to trigger by accident.
* **Eval commands**: These entries may contain arbitrary javascript code on which tbkeys will call `eval()` when the key binding is triggered.
Any entry not matching the prefixes of the other command types is treated as an eval command.
**NOTE:** eval commands are not available in tbkeys-lite.

## Common keybindings

Here are some examples of eval commands for commonly desired keybindings:

* **Next tab**: `window.document.getElementById('tabmail-tabs').advanceSelectedTabs(1, true)`
* **Previous tab**: `window.document.getElementById('tabmail-tabs').advanceSelectedTabs(-1, true)`
* **Close tab**: `window.document.getElementById('tabmail-tabs').selectedItem.getElementsByClassName('tab-close-button')[0].click()`
* **Scroll message list down**: `window.document.getElementById('threadTree').scrollByLines(1)`
* **Scroll message list up**: `window.document.getElementById('threadTree').scrollByLines(-1)`
* **Scroll message body down**: `window.document.getElementById('messagepane').contentDocument.documentElement.getElementsByTagName('body')[0].scrollBy(0, 100)`
* **Scroll message body up**: `window.document.getElementById('messagepane').contentDocument.documentElement.getElementsByTagName('body')[0].scrollBy(0, -100)`
* **Create new folder**: `goDoCommand('cmd_newFolder')`
* **Subscribe to feed**: `window.openSubscriptionsDialog(window.GetSelectedMsgFolders()[0])`

## tbkeys and tbkeys-lite

tbkeys-lite is a version of tbkeys with the ability to execute arbitrary javascript removed.
