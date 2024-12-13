# JetBrains IDE (IntellJ) Menubar

**VERY VERY IMPORTANT**: Use at your own risk. This is not a officially supported way to patch the IDE and your warranty may be voided. So use it at your own risk and if you know what you are doing.

## Background

JetBrains IDEs like IntellIJ now use what is deemed as NewUI - which is now the default. It has a nice feature of displaying the menubar in the toolbar. The toolbar has sections:

- Left
- Center
- Right

where various actions and action groups can be added. The left are extends all the way to the left of the window. Therefore when the menubar is shown in the toolbar they came up with a `hamburger` button. The full menubar is shown when the user clicks on the `hamburger` button. The menu covers the actions on the toolbar - especially the actions in the left section. But as soon as you invoke any action from the menubar it collapses again. I can see why they did that so that the actions in the left section. The good news is that the toolbar is customizable and all the actions can be moved to the right section. Besides most developers have wide enough monitors, such that horizontal space is not an issue on the toolbar. However the behviour is annoying. IMO it is a UX mistake. It should have been a opt-in feature to collapse the menubar after use. Or at minimum it should have been an opt-in to keep it expanded - and let the user move actions to the right side of the toolbar.


## Youtrack issue

Users did not like the new behavior and this issue was filed:

[New UI: allow showing expanded menu and other toolbar widgets in window header](https://youtrack.jetbrains.com/issue/IJPL-43725)

I even comented on it. I got annoyed enough that I checked out the IntellJ Community edition and fixed it by introducing an opt-in flag:


`ide.main.menu.expand.horizontal.sticky=true`

Default value is false to keep the native behavior. Setting it to true will keep the menubar showing and not collapse it back into `hamburger` button.


to be set in the IDE custom properties via the **Help > Edit custom properties...** action. BTW the existing flag:

`ide.main.menu.expand.horizontal=true`

controls if the menubar is shown horizontally or as a pop-up menu. Thus my flag simply is a addtion to that.

I even submitted a Pull request:

[IJPL-43725: New UI: allow showing expanded menu and other toolbar wid…](https://github.com/JetBrains/intellij-community/pull/2889)

However they have not accepted it yet. In the meantime they updated the issue indicating that they will fix it in 2025.1 release.

## Patch

I really do not like the collapsing menubar. So I created this patch reporitory.

### How to apply the patch

- **IMPORTANT: DO THIS FIRST** - Use **Help > Edit custom properties...** action and set flag:

```properties
:
ide.main.menu.expand.horizontal.sticky=true
:
```

- Edit the `bin/idea.sh` file ( sample is included in `bin/idea.sh`) and make sure to add:

```bash
:
CLASS_PATH="$CLASS_PATH:$IDE_HOME/lib/app-client-pre.jar"
CLASS_PATH="$CLASS_PATH:$IDE_HOME/lib/app-client.jar"
:
```
  which basically puts the patched classes in front to the original.


NOTE: You can do similar changes to the `bin\idea.bat` file on windows.

```bat
:
SET "CLASS_PATH=%CLASS_PATH%;%IDE_HOME%\lib\app-client-pre.jar"
SET "CLASS_PATH=%CLASS_PATH%;%IDE_HOME%\lib\app-client.jar"
:
```

- Drop the `lib/app-client-pre.jar` in the IDE's lib folder.

This file contains:

```
 16501 com/intellij/openapi/wm/impl/customFrameDecorations/header/toolbar/ExpandableMenu.class <----------- This has the patched behavior
  1416 com/intellij/openapi/wm/impl/customFrameDecorations/header/toolbar/ExpandableMenu$2.class
  3152 com/intellij/openapi/wm/impl/customFrameDecorations/header/toolbar/ExpandableMenu$HeaderColorfulPanel.class
  1890 com/intellij/openapi/wm/impl/customFrameDecorations/header/toolbar/ExpandableMenu$ShadowComponent.class
  1447 com/intellij/openapi/wm/impl/customFrameDecorations/header/toolbar/ExpandableMenu$ShadowComponent$1.class
   442 com/intellij/openapi/wm/impl/customFrameDecorations/header/toolbar/ExpandableMenuKt.class
```

- Of course make sure to launch the IDE using `bin/idea.sh` (on linux and mac).
