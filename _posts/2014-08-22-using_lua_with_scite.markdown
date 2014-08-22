---
layout: post
title: "Using_Lua_With_Scite"
date: 2014-08-22 09:50
category:
tags: [SciTE]
---
{% include JB/setup %}

# Using Lua With Scite

Any global Lua function can be used as a SciTE command. These functions appear on the Tools Menu, and may have an associated shortcut key.
任何的 Lua 函式都可以當成 SciTE 的指令，這些函式會顯示在Tools的下拉選單中，也可以用快捷鍵叫用。

Here is a simple example; put this in your properties file (could be local,user or global):
這是一個簡單的範例，將這幾行放在你的設定檔中
```
command.name.1.*=Load Lua
command.subsystem.1.*=3
command.1.*=dofile $(FilePath)
```

You will now have a menu item called 'Load Lua' on the Tools menu, and by default it will have a shortcut Ctrl+1. command.subsystem must be set to 3, which is the Lua extension.
> 現在，在你的Tools下拉選單中將會有一個選單項目叫做 **Load Lua**，而且它會有一個預設的快捷鍵 **Ctrl+1**。command.subsystem 必須為3，這代表 **.lua** 副檔名。

In this case, the global Lua function is dofile which has been kept in SciTE's Lua 5 precisely because it's so useful in this case;
> 在這個範例中，dofile 這個廣域的Lua 函式已經被保留在 Lua 5 中，因為它是非常有用的。

please note that there is no argument list after dofile. There can be at most one argument passed to your function, but like anything in a SciTE properties file, it can contain references to other SciTE properties, like FilePath, which is the full path of the currently shown file. (See the section called 'Properties File' in the SciTE manual for a full list of these dynamic properties)
> 注意，dofile 之後沒有參數列表，最多只能傳遞一個參數給你的函式，但就像在SciTE設定檔中的任何事，它可以參考到其他SciTE設定值，像是 FilePath，這是目前檔案的完整路徑。(完整的動態設定值列表可以查看 SciTE 手冊的 Properties File章節)

Now, if you edit a file containing this:
  `print ("Hello, World!")`
and pressed Ctrl+1, "Hello, World" will be shown in the SciTE output window. If you have modified this file, then SciTE will prompt you first to save; this is the default behaviour. This is a powerful way to begin Lua programming in the SciTE environment.
> 現在，你可以編輯一個檔案，包含以下內容：
    `print ("Hello, World")`
然後按 Ctrl+1，**Hello, World** 將會出現在 SciTE 的 Output 視窗。如果你曾經修改這個檔案，SciTE 預設會提醒你要先存檔。這是在 SciTE 環境下開始學習 Lua 程式開發的好方法。

There is a potential problem with this definition; some other script may have defined the command 1; you can have commands from 0 to 9 with this method, bound to Ctrl+0 .. Ctrl+9. But you can use any number less than 50 in these definitions, as long as you give an explicit shortcut key:
> 這個做法有一個潛在的問題；也許有其他的腳本已經定義了指令1；你可以將這個方法定義為 0~9中的任一個，你可以使用50以下的任一個數字，直到你得到一個快捷鍵。

```
command.name.11.*=Load Lua
command.subsystem.11.*=3
command.11.*=dofile $(FilePath)
command.mode.11.*=savebefore:yes
command.shortcut.11.*=F9
```

You can rebind a key that has already been defined by SciTE or Scintilla. There are a lot of these, so consult the documentation.
> 你可以將已經被 SciTE or Scitilla 使用的鍵重新綁定，這樣的鍵有很多，所以要先查閱文件

Here is an alternative which doesn't require the document to be on disk - it executes all the source in the current buffer:
> 以下做法是另一種選擇，不須要文件存在硬碟中，它執行目前緩衝區中的所有內容。

```
command.name.1.*=Run Document as Lua Extension
command.subsystem.1.*=3
command.1.*=dostring dostring(editor:GetText())
command.mode.1.*=savebefore:no
```

## A simple SciTE Macro


Put this in a Lua file (say test.lua) in your home directory
> 將以下內容在成 **test.lua** 放在你的 **home** 目錄

```
function make_uppercase()
  local sel = editor:GetSelText()
  editor:ReplaceSel(string.upper(sel))
end
```

and in your properties file, put this
> 在你的設定中，輸入以下內容

```
ext.lua.startup.script=$(SciteUserHome)/test.lua

command.name.12.*=Make Selection Uppercase
command.subsystem.12.*=3
command.12.*=make_uppercase
command.mode.12.*=savebefore:no
command.shortcut.12.*=Ctrl+M
```
Now, after selecting text, you can make it uppercase with Ctrl+M. Because of the savebefore:no, it won't prompt you to save first before executing.
> 現在，在選擇文字後，你可以用 **Ctrl+M** 將它變成大寫。因為 savebefore 設定為 no，將不會提醒你在執行前要先儲存。

OK, it's true that SciTE already has such an operation. But you can do just about anything with Lua in SciTE! I would suggest that a good way to learn is to experiment using the dofile trick.
> 在 SciTE 中已經有這樣的功能，但你可以用**Lua**在**SciTE**中做任何事。

## Looking up the correct form of a Scintilla function
## 了解 Scitilla 函式的正確格式

The best reference for the Lua Scintilla bindings is scintilla.iface, which is in the Scintilla include directory. If you look for GetLength you will find:
> 了解 Scitilla 和 Lua 如何配合 最好的參考是 **scitilla.ifacw**，它在 Scitilla 的 include 目錄下。如果你查看 **GetLength**，你將會發現：

 **Returns the number of characters in the document.**
 `get int GetLength=2006(,)`


The 'get' means that there is a read-only property called Length. It will be called like this: editor.Length.
> **get** 表示 **取得長度** 這個函式是唯讀屬性。它可以這樣呼叫：`editor.Length`

whereas looking for GetText we get:
> 再來看 **GetText**，我們會發現：

 **Retrieve all the text in the document.**
 **Returns number of characters retrieved.**
 `fun int GetText=2182(int length, stringresult text)`

So GetText is a plain function, which is passed a string. It will be editor:GetText() - note the colon!
> 所以 **GetText** 是一個普通的函式，傳回一個字串。
它的用法是 **editor::GetText()** - 注意冒號！

The Lua bindings are not always consistent, for example GetSelText is a function, not a property.
> Lua 的綁定並不總是一致的，例如 **GetSelText** 是一個函式，而非屬性。

An annotated version of the Scintilla documentation for the Lua interface can be found here: http://scite-interest.googlegroups.com/web/ScintillaSciteDoc.html
> 針對 Lus 介面的 Scitilla 有註解版文件可[在此取得](http://scite-interest.googlegroups.com/web/ScintillaSciteDoc.html)

## Getting and Modifying Document Text
## 取得與變更文件的文字

editor:GetText() will return the full text of the current document, and editor:SetText(s) will replace the current contents of the document with the string s.
> **editor:GetText()** 會取得目前文件的全部內容，**editor:SetText(s)** 會將目前文件的內容 換成字串 s。

editor:GetLine(n) will get all the text of the line n, including any end-of-line characters. Remember that under Windows there will be two of these ('\r\n'); all line numbers are zero-based. The following is a simple function to remove the end-of-line characters:
> **editor:getLine(n)** 會取得第n行的全部文字，包括換行字元。在 Windows下這將是 **\r\n** 2個字元；行號是從0開始，下列是一個簡單的函式，用來移除換行字元。

```
  -- removes end-of-line characters in a string
  function Chomp(line)
    return string.gsub(line, "[\r\n]+$", "")
  end
```

editor:GetSelText() will retrieve the currently selected text.
> **editor:GetSelText() 將會取回目前被選擇的文字。

The length of the document in characters is editor.Length and in lines is editor.LineCount; note the different syntax used here, since Length and LineCount are properties. Another example is editor.CharAt[p] which will get the character at position p. This will be the character code, so use string.char(ch) to generate a string:
> **editor.Length** 取得文件的字數，**editor.LineCount** 取得行數。注意，這裡使用不用的語法，Length 和 LineCount 是屬性。另一個範例是 **editor.CharAt[p]**，這將會取回位置 p 的字元。這將是此字元的編碼，所以使用 **string.char(ch)** 來產生一個字串。

```
  -- returns the character at position p as a string
  function char_at(p)
     return string.char(editor.CharAt[p])
  end
```

editor:textrange(p1,p2) will get the text between p1 and p2; (this is a SciTE pane function). So an alternative way to get the character as a string at a position p is editor:textrange(p,p+1).
> **editor:textrange(p1,p2)** 將會取得 p1 到 p2 間的文字；這是 SciTE 的內建(窗格)函式，所以另一個取得位置p字元轉成字串的方法是 **editor:textrange(p,p+1)**

editor:ClearAll() will clear the document.
> **editor:ClearAll()** 將會清空文件

editor:AppendText(s) will append s to the end of the document, and editor:InsertText(p,s) will insert s at the position p; a position of -1 means the current position. This is where the caret is currently displayed; in all cases, please note that InsertText won't scroll the text into view. editor:ScrollCaret() will do that for you.
> **editor:AppendText(s)** 會將字串 s 附加在目前文件的最後，**editor:InsertText(p,s)** 會將字串 s 插入位置 p；p = -1 則表示插入到目前的位置。注意，插入文字將不會捲動視窗文字。 **editor:ScrollCaret()** 才會捲動。

editor:ReplaceSel(s) will replace the selection with s. Here is a function that will enclose the selected text in bold tags:
> **editor:ReplaceSel(s)** 會將被選的文字替換成 s，下列函式會將被選到的文字以粗体字標籤包起來。

```
  function make_bold()
    local txt = editor:GetSelText();
    editor:ReplaceSel('<b>'..txt..'</b>')
  end
```

## Selection and Position Information

To move to a new position, use editor:GotoPos(p) or editor:GotoLine(l). They will always make the caret visible.
> 要移動到新的位置，可以使用 **editor:GotoPos(p)** 或是 **editor:GotoLine(l)**。它們都會讓游標顯示出來。

Given a position p, editor:LineFromPosition(p) will give you the line, and editor:PositionFromLine(l) will give you the position at the start of the line. If you need the position at line end, use editor.LineEndPosition[l] (note that properties are accessed as if they were arrays).
> 提供一個位置 p， **editor:LineFromPosition(p)** 會給你它所在的行。 **editor:PositionFromLine(l)** 給你此行第一個字的位置，若是你需要行尾的位置，使用 **editor.LineEndPosition[l]** (注意這些屬性可以像array一樣來存取)。

editor.CurrentPos will return the current caret position; this is a writeable property, so editor.CurrentPos = p also works, but it doesn't have the same meaning as editor:GotoPos(p). The selection in Scintilla is between the anchor and the position, so if there was an existing selection, then setting the position directly would change the selection. editor:SetSel(p1,p2) is the best way to explicitly set the selection.
> **editor.CurrentPos** 會返回目前游標所在的位置，這是一個可改寫的屬性，所以 `editor.CurrentPos = p` 是允許的。但是它和 **editor:GotoPos(p)** 是不同意義的。在 **Scitilla** 中的被選區是在起始點(錨定點)和目前游標位置之間，所以如果已經有被選區，則直接設定游標位置將會改變被選區。 **editor:SetSel(p1,p2)** 是一個明確設定被選區的好方法。

To find out the currently visible part of the document, use editor.FirstVisibleLine to find out the starting line number, and editor.LinesOnScreen to find out the number of lines visible on the page.
> **editor.FirstVisibleLine** 可以找出目前文件可見部份的起始行， **editor.LinesOnScreen** 可以找出這一頁可顯示的行數。

center_pos() is a useful function that uses this information to center the display around a position.
> **center_pos()** 是一個有用的函式，用來將目前的游標所在行移到畫面正中間

```
 -- this centers the cursor position
 function center_pos(line)
  if not line then
     -- this is the current line
     line = editor:LineFromPosition(editor.CurrentPos)
  end
  local top = editor.FirstVisibleLine
  local middle = top + editor.LinesOnScreen/2
  editor:LineScroll(0,line - middle)
 end
 ```

## SciTE Properties

There is a pseudo-array called props which can access any defined SciTE property. For example, props['FilePath'] will give you the full path to the file currently being edited. Here is a very simple function which will swap a C++ file with its header, assuming that the extensions are only .cpp and .h:
> 有一個虛擬的 Array 叫做 **props**，它可以用來存取任何被定義的 SciTE 屬性。例如， **props['FilePath']** 會給你目前正在編輯的檔案的完整路徑。這裡有一個非常簡單的函式，用來交換 C++ 檔案和它的標頭檔。假設副檔名只有 **.cpp** 和 **.h** :

```
  function swap_header()
    local cpp_ext = 'cpp'
    local h_ext = 'h'
    local f = props['FileName']    -- e.g 'test'
    local ext = props['FileExt']   -- e.g 'cpp'
    local path = props['FileDir']  -- e.g. '/home/steve/progs'
    if ext == cpp_ext then
       ext = h_ext
    elseif ext == h_ext then
       ext = cpp_ext
    end
    scite.Open(path..'/'..f..'.'..ext)
  end
```

Please see the section called 'Properties File' in the SciTE documentation for a full list of properties set by the environment.
> 整個環境的完整屬性列表請查閱 SciTE 文件的 **Properties File** 章節。

Remember that parameters as defined by View|Parameters are accessable as prop[1],prop[2],prop[3] and prop[4].
> 記得在 **檢視|參數(P)** 中的參數是以 prop[1]~prop[4]來存取。

You can of course access any defined properties, for example props['position.height'] will give the initial height of the SciTE window. Special properties may be defined which are meant to be read only by scripts. To make swop_header() more general, define a property called 'cpp.swop.ext' to be your C++ source extension of choice and set cpp_ext to this.
> 你當然可以存取任何被定義的屬性，例如 **props['position.height']** 將會取得 SciTE 視窗的初始高度。 特殊的屬性會被定義為腳本只能讀取。讓 **swop_header()** 更為通用化, 可以定義一個屬性 **cpp.swop.ext** 當做 C++ 原始碼副檔名，而且設定 cpp_ext 等於它。

  `local cpp_ext = props['cpp.swop.ext']`
  ...

Then define 'cpp.swop.ext=cxx' (or whatever) in your Local properties file.
> 然後定義 **cpp.swop.ext=cxx** (或其他名稱)在你的當地屬性檔。

It is possible for a script to change properties, although of course this will be only temporary. Here is something that makes life easier for script developers; normally, each Lua function to be called needs to be specified in a property file, but there's no reason why those properties can't be autogenerated. Here is the important part of scite_Command from SciteExtMan.
> 腳本可以改變屬性，當然只是臨時的。這裡有一些東西讓腳本開發員的生活好過一些；通常，每一個被呼叫的 Lua 函式必須要被設定在一個屬性檔中，但沒有理由那些屬性不能被自動產生。下列程式是 [SciTEExtMan](http://lua-users.org/wiki/SciteExtMan) 中關於 scite_command 很重要的部份

     -- we are fed something like 'Process File|ProcessFile|Ctrl+M'
     local name,cmd,shortcut = split3(v,'|')
     local which = '.'..idx..'.*'
     props['command.name'..which] = name
     props['command'..which] = cmd
     props['command.subsystem'..which] = '3'
     props['command.mode'..which] = 'savebefore:no'
     props['command.shortcut'..which] = shortcut

## Searching and Replacing

To find text in the current document, use editor:findtext(). It returns two positions representing the returned range, nil if no match is possible. This function prints out all lines having some given text:
> 使用 **editor:findtext()** 在目前的文件中尋找文字。它返回2個位置代表找到的區域，**nil** 表示沒有符合的。這個函式列出所有包含此文字的行。

```
  function all_lines_with_text(txt,flags)
    if not flags then flags = 0 end
    local s,e = editor:findtext(txt,flags,0)
    while s do
      local l = editor:LineFromPosition(s)
      trace(l..' '..editor:GetLine(l))
      s,e = editor:findtext(txt,flags,e+1)
    end
  end
```

(Here I'm using trace() instead of print() because the line already has a line feed)
> 這裡使用 trace() 取代 print()，因為這一行已經有一個換行符號了。

The search flags are a combination of SCFIND_MATCHCASE, SCFIND_WHOLEWORD, SCFIND_WORDSTART, and SCFIND_REGEXP. By default, the search is a plain case-sensitive search. all_lines_with_text('for',SCFIND_WHOLEWORD) would show all for-statements in a C file, all_lines_with_text('^#',SCFIND_REGEXP) would show all preprocessor statements (i.e. all occurances of '#' which occur at the start of the line). Please note that SciTE regular expressions are different from Lua's - see 'Searching' in http://scintilla.sourceforge.net/ScintillaDoc.html for details.
> 這個搜尋的 flag 包含 **SCFIND_MATCHCASE, SCFIND_WHOLEWORD, SCFIND_WORDSTART, and SCFIND_REGEXP** 的組合。預設是普通的區分大小寫的搜尋。
**all_lines_with_text('for',SCFIND_WHOLEWORD)** 將會顯示一個 C 檔案全部的 for 語句，
**all_lines_with_text('^#',SCFIND_REGEXP)** 將會顯示所有的預處理語句 (例如，所有以 **#** 開始的行)。
注意， SciTE 正則式語法和 Lua 是不同的。細節請查看 [ScintillaDoc](http://scintilla.sourceforge.net/ScintillaDoc.html) 中的 **Searching**。

The easiest way to do a search-and-replace is using editor:match(), which gives us an iterator:
> 搜尋和替代最簡單的方法是使用 **editor:match()**，它給我們一個迭代器

```
  function replace_all(target,repl)
    editor:BeginUndoAction()
    for m in editor:match(target) do
      m:replace(repl)
    end
    editor:EndUndoAction()
  end
```

Using BeginUndoAction() is the general way to make sure that a number of changes can be undone at once.
> 使用 **BeginUndoAction()** 是確保多個修改可以被還原的常見方法，。

## Markers

SciTE uses markers to implement things like bookmarks and for marking lines with errors. There are 32 possible markers, and Scintilla makes markers 0 to 24 available for general use; SciTE uses 0 for error lines and 1 for bookmarks. For example, editor:MarkerAdd(line,1) will put a bookmark at line, and SciTE will treat it just like any other bookmark, since it finds bookmarks using the internal Scintilla list. Please remember, as always, that Scintilla counts lines from zero.
> SciTE 使用 markers 來實作出書籤和標示錯誤行。有32種markers，Scintilla 使用 0~24 的 markers，SciTE 使用 0 為錯誤行標示，1 為書籤。例如，**editor:MarkerAdd(line,1)** 會放一個書籤在 line, SciTE將會視它為一般的書籤，因為它使用 Scintilla 內部列表來尋找書籤。記得，Scintilla 從 0 開始計算行數。

Here is a useful function for defining a custom marker:
這裡有一個用來設定自定義 marker 的有用函式

```
local colours = {red = "#FF0000", blue = '#0000FF', green = '#00FF00', pink ="#FFAAAA" , black = '#000000', lightblue = '#AAAAFF', lightgreen = '#AAFFAA'}

  function colour_parse(str)
    if sub(str,1,1) ~= '#' then
      str = colours[str]
    end
    return tonumber(sub(str,6,7)..sub(str,4,5)..sub(str,2,4),16)
  end

  function marker_define(idx,typ,fore,back)
    editor:MarkerDefine(idx,typ)
    if fore then editor:MarkerSetFore(idx,colour_parse(fore)) end
    if back then editor:MarkerSetBack(idx,colour_parse(back)) end
  end
```

## User Lists

These are drop-down lists which allow the user to choose from a number of items, which SciTE uses for 'Complete Symbol', etc. They are not difficult to use; you supply a string with a specified separator character, and the OnUserListSelection event is fired when the user selects an item.
> 下拉式選單允許使用者從多個項目中選擇，其中，SciTE 使用完整符號，等等。它們並不難使用，你提供一個包含特殊分隔符號的字串，當使用者選擇一個項目時， **OnUserListSelection** 會被啟動。

```
 function UserListShow(list)
   local s = ''
   local sep = ';'
   local n = table.getn(list)
   for i = 1,n-1 do
      s = s..list[i]..sep
   end
   s = s..list[n]
   editor.AutoCSeparator = string.byte(sep)
   editor:UserListShow(12,s)
   editor.AutoCSeparator = string.byte(' ')
 end
```

The tricky thing here is that the property AutoCSeparator is passed a character code, not a string. The '12' is just a number that SciTE doesn't use internally.
> 棘手的事情是 **AutoCSeparator** 是傳入字元碼，不是字串。 **12** 只是一個 SciTE 內部沒有使用的數字。

Here is an event handler which assumes that the strings represent Lua scripts in some directory. The idea here is to present the user with a list of little-used 'once-off' scripts, which would otherwise clutter the Tools menu.
> 這裡是一個事件處理函式，它假設 Lua 腳本傳入的字串是一些目錄。這裡的想法是顯示給使用者的是一次性的選單腳本，否則會弄亂 Tools 的選單。

```
 function OnUserListSelection(tp,script)
   if tp == 12 then
      dofile(path..'/'..script..'.lua')
   end
 end
```

Building up the list of files requires a little work. Here is a non-Windows solution to this problem:
> 建立檔案列表需要花一點工夫。這裡是針對這個問題的無視窗解法。

```
 function GetFiles(mask)
   local files = {}
   local tmpfile = '/tmp/stmp.txt'
   os.execute('ls -1 '..mask..' > '..tmpfile)
   local f = io.open(tmpfile)
   if not f then return files end
   local k = 1
   for line in f:lines() do
      files[k] = line
      k = k + 1
   end
   f:close()
   return files
 end
```

Code that works on both Unix and Windows is a little tricky. See scite_Files() in SciteExtMan for a more complete solution.
> 程式碼要同時在 Unix 和 Windows 上會有些問題，[SciteExtMan](http://lua-users.org/wiki/SciteExtMan) 的 scite_Files() 有比較完整的解法。

## Indicators

Indicators are not currently used in SciTE, but scripts can easily add them. They could be used by a spell check utility to underline misspelt words with a red line, or dodgy syntax with a squiggly green line. There are three available indicators with the usual SciTE settings. Here is a function which will use the given indicator (0,1 or 2) to underline len characters starting at pos.
> 指示器目前沒有在 SciTE 上使用，但腳本很容易加上它們。它們可以用在拼字檢查工具上，在拼錯的字下面劃紅線，或是在錯誤的語法劃上彎曲的綠線。 在一般的 SciTE 設定中，有3種指示器可用。底下是一個使用指示器劃底線的函式。

```
 function underline_text(pos,len,ind)
   local es = editor.EndStyled
   editor:StartStyling(pos,INDICS_MASK)
   editor:SetStyling(len,INDIC0_MASK + ind)
   editor:SetStyling(2,31)
 end
```

To remove underlining, use underline_text(pos,len,-1). The last SetStyling() call is necessary to restore the lexer state; 31 is the mask for the lower 5 bits, which are used for styling. The defaults can be changed, if necessary.
> 要移除底線，使用 **underline_text(pos,len,-1)**。最後呼叫 **SetStyling()** 是為了回復原本的 lexer 狀態； **31** 是為了和低位元的 5 個 bits 做 mask，那是使用於樣式。如果需要，可以更改預設值。

#### The default indicators are 預設的指示器如下

   0 green squiggly line 綠色彎曲線
   1 light blue line of small T shapes 小T形的亮藍線
   2 light red line 亮紅線

#### The available indicator styles are 可用的指示器樣式為

  INDIC_PLAIN   Underlined with a single, straight line.
  INDIC_SQUIGGLE  	A squiggly underline.
  INDIC_TT  A line of small T shapes.
  INDIC_DIAGONAL  	Diagonal hatching.
  INDIC_STRIKE  	Strike out.
  INDIC_HIDDEN	An indicator with no visual effect.
  INDIC_BOX 	A rectangle around the text.

## Indicators with version 1.70 and maybe previous

#### You have these flags available :

   0 resets the style
   INDIC0_MASK green word processing-like line
   INDIC1_MASK bizarre blue line
   INDIC2_MASK blue round background box

#### And you case use it like this :

editor:StartStyling(editor.SelectionStart,INDICS_MASK)
editor:SetStyling(string.len(editor:GetSelText()),flag)

