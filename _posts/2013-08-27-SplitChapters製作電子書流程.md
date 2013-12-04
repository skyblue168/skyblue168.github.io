---
layout: post
title: 利用SplitChapters 及 eCub 製作電子書的步驟
category: "說明"
tags: [epub, txt, 電子書]
---

## [SplitChapters 程式下載](/bin/SplitChapters.exe)
## [eCub 程式下載](http://www.juliansmart.com/ecub#windows)

 1. 將要製作為電子書的文字檔放在自訂的目錄，此目錄中的 txtsrc 子目錄要清空或是刪除。  
 2. 執行 SplitChapters.exe，選擇該文字檔，再依文字檔的編碼方式勾選 UTF8 或是 GB(簡体字)，ANSI/BIG5 則不須勾選。  
 3. 按確定，開始切分章節，完成後會顯示共分了多少章。  
 4. 進入 txtsrc 子目錄，查看 0.txt，可檢查每一章節的目錄是否正確。可將多餘的、只有章節名稱的檔案刪除。
 5. 將要當做封面的圖檔取名為 cover.jpg，一併放入 txtsrc 目錄中。
 6. 執行 eCub 程式，選擇 Create new project，填入書名、代碼、 作者，即可按 Next
 7. 第二頁輸入檔名，選擇 txtsrc 為 Project file folder，按 Next，中間都不須修改，直到 Content 頁面
 8. 在 Content 頁面，選擇要包含到書中的檔案，不須勾選 0.txt，會自動產生目錄
 9. 之後一直按到 finish，回到 eCub 主頁面，按下工具列中的 Compile，即可產出 epub 檔



