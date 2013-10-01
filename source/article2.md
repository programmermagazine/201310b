## JavaScript (10) – Google 的語音辨識 API 之使用 (作者：陳鍾誠)

在上期「程式人雜誌」當中，我們介紹了的語音合成的主題，也就是如何讓網頁念出中文或英文，文章網址如下：

* [JavaScript (9) – Google 的語音合成 API 之使用](https://dl.dropboxusercontent.com/u/101584453/pmag/201309/htm/article2.html)

本期中，我們將介紹如何製作可以進行語音辨識的網頁，同樣也是利用 Google 的服務。但不同的是，目前
好像只有 Chrome 瀏覽器之援這個功能。

### 簡介

2012 年，W3C 釋出了 [W3C:Web Speech API Specification] 這份文件，讓瀏覽器可以具備語音辨識的功能，而 Google 的 Chrome
在第 25 beta 版當中，就開始支援語音辨識功能了，這個功能的主角是一個稱為 webkitSpeechRecognition 的物件，用法如下所示。

```javascript
  var recognition = new webkitSpeechRecognition();
  recognition.continuous = true; // 連續辨識
  recognition.interimResults = true; // 是否要輸出中間結果
  recognition.onstart = function() { ... } // 開始辨識時會自動呼叫這個函數
  recognition.onend = function() { ... }   // 辨識完成時會自動呼叫這個函數
  recognition.onresult = function(event) { ... } // 辨識有任何結果時會呼叫這個函數
  ...
```

透過這個物件，我們就能輕易的寫出語音辨識的 HTML+JavaScript 程式了。

### 範例程式

以下是筆者根據 Google 釋出的 [Web Speech API Demonstration] 這個範例所修改而來的一個簡化範例，其執行畫面如下。

![圖、本程式的英文的語音辨識結果](../img/speechToText_en.png)

![圖、本程式的中文的語音辨識結果](../img/speechToText_tw.png)

以下是筆者自己使用這個程式的錄影示範，您可以先看完之後再來使用這個範例，可能會比較順利。

* <http://www.youtube.com/watch?v=EAYSJ6JbkFU>

必須注意的是，當您直接將以下範例放在「電腦硬碟」當中執行時，是會因為安全性問題而被檔下的，因此您必須將此範例
放到 Web Server 上或 Dropbox 等環境下，然後再用 Google Chrome 25 版之後的瀏覽器開啟執行，這樣就可以正常運作了。

檔案：speechToText.html

網址：<https://dl.dropboxusercontent.com/u/101584453/pmag/201310/code/SpeechToText.html>

```html
<html>
<head><meta charset="utf-8" /></head>
<body>
<script type="text/javascript">
var infoBox; // 訊息 label
var textBox; // 最終的辨識訊息 text input
var tempBox; // 中間的辨識訊息 text input
var startStopButton; // 「辨識/停止」按鈕
var final_transcript = ''; // 最終的辨識訊息的變數
var recognizing = false; // 是否辨識中

function startButton(event) {
  infoBox = document.getElementById("infoBox"); // 取得訊息控制項 infoBox
  textBox = document.getElementById("textBox"); // 取得最終的辨識訊息控制項 textBox
  tempBox = document.getElementById("tempBox"); // 取得中間的辨識訊息控制項 tempBox
  startStopButton = document.getElementById("startStopButton"); // 取得「辨識/停止」這個按鈕控制項
  langCombo = document.getElementById("langCombo"); // 取得「辨識語言」這個選擇控制項
  if (recognizing) { // 如果正在辨識，則停止。
    recognition.stop();
  } else { // 否則就開始辨識
    textBox.value = ''; // 清除最終的辨識訊息
    tempBox.value = ''; // 清除中間的辨識訊息
    final_transcript = ''; // 最終的辨識訊息變數
    recognition.lang = langCombo.value; // 設定辨識語言
    recognition.start(); // 開始辨識
  }
}

if (!('webkitSpeechRecognition' in window)) {  // 如果找不到 window.webkitSpeechRecognition 這個屬性
  // 就是不支援語音辨識，要求使用者更新瀏覽器。 
  infoBox.innerText = "本瀏覽器不支援語音辨識，請更換瀏覽器！(Chrome 25 版以上才支援語音辨識)";
} else {
  var recognition = new webkitSpeechRecognition(); // 建立語音辨識物件 webkitSpeechRecognition
  recognition.continuous = true; // 設定連續辨識模式
  recognition.interimResults = true; // 設定輸出中先結果。

  recognition.onstart = function() { // 開始辨識
    recognizing = true; // 設定為辨識中
    startStopButton.value = "按此停止"; // 辨識中...按鈕改為「按此停止」。  
    infoBox.innerText = "辨識中...";  // 顯示訊息為「辨識中」...
  };

  recognition.onend = function() { // 辨識完成
    recognizing = false; // 設定為「非辨識中」
    startStopButton.value = "開始辨識";  // 辨識完成...按鈕改為「開始辨識」。
    infoBox.innerText = ""; // 不顯示訊息
  };

  recognition.onresult = function(event) { // 辨識有任何結果時
    var interim_transcript = ''; // 中間結果
    for (var i = event.resultIndex; i < event.results.length; ++i) { // 對於每一個辨識結果
      if (event.results[i].isFinal) { // 如果是最終結果
        final_transcript += event.results[i][0].transcript; // 將其加入最終結果中
      } else { // 否則
        interim_transcript += event.results[i][0].transcript; // 將其加入中間結果中
      }
    }
    if (final_transcript.trim().length > 0) // 如果有最終辨識文字
        textBox.value = final_transcript; // 顯示最終辨識文字
    if (interim_transcript.trim().length > 0) // 如果有中間辨識文字
        tempBox.value = interim_transcript; // 顯示中間辨識文字
  };
}
</script>	
<BR/>
最後結果：<input id="textBox" type="text" size="60" value=""/><BR/>
中間結果：<input id="tempBox" type="text" size="60" value=""/><BR/>
辨識語言：
<select id="langCombo">
  <option value="en-US">英文(美國)</option>
  <option value="cmn-Hant-TW">中文(台灣)</option>
</select>
<input id="startStopButton" type="button" value="辨識" onclick="startButton(event)"/><BR/>
<label id="infoBox"></label>
</body>
</html>
```

### 結語

透過 webkitSpeechRecognition 這個物件，我們可以用不是很長的程式碼，完成語音辨識的功能，當然也可以將這個功能進一步包裝
讓大家更方便使用，而不需要每次都寫幾十行程式碼。

如果您想更進一步瞭解 webkitSpeechRecognition 這個物件，可以參考以下 Google 釋出的專案，這個專案的介面較美觀，支援更多語言，
但是程式碼也較多，建議您先讀懂上述範例的寫法之後，再來閱讀這個專案。

* [Web Speech API Demonstration]
    * [GoogleChrome/webplatform-samples : 上述範例的原始碼]
    
原本、筆者認為 Web 程式要做語音辨識是一項很困難的任務，但在 Web 越來越發達的時代，似乎甚麼樣困難的任務都變得很簡單了，
或許再過不久，所有的程式都可以用 Web 的方式製作了也不一定。

### 參考文獻
* [Voice Driven Web Apps: Introduction to the Web Speech API](http://updates.html5rocks.com/2013/01/Voice-Driven-Web-Apps-Introduction-to-the-Web-Speech-API), By Glen Shires at 14 January, 2013
* [Web Speech API Demonstration]
    * [GoogleChrome/webplatform-samples : 上述範例的原始碼]
* [W3C:Web Speech API Specification, 19 October 2012]
* [The WebSpeech API Enables Voice Recognition and Speech Synthesis in the Browser](http://badassjs.com/post/40534144131/the-webspeech-api-enables-voice-recognition-and-speech)
* [Textarea value Property](http://www.w3schools.com/jsref/prop_textarea_value.asp), W3Schools.

[W3C:Web Speech API Specification, 19 October 2012]:https://dvcs.w3.org/hg/speech-api/raw-file/tip/speechapi.html
[Web Speech API Demonstration]:https://www.google.com/intl/en/chrome/demos/speech.html
[GoogleChrome/webplatform-samples : 上述範例的原始碼]:https://github.com/GoogleChrome/webplatform-samples/blob/master/webspeechdemo/webspeechdemo.html

