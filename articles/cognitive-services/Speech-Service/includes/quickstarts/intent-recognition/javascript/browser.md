---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 77fab9c4a52a1d54f548dfbcb35e351195d13bd9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052747"
---
## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt.

```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Microsoft Cognitive Services Speech SDK JavaScript Quickstart</title>
    <meta charset="utf-8" />
    </head>
    <body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
    </body>
    </html>
```
## <a name="add-ui-elements"></a>Přidat prvky uživatelského rozhraní

Nyní přidáme několik základních uživatelských rozhraní pro vstupní pole, odkázat na JavaScript SDK pro rozpoznávání řeči a pokud je k dispozici, vytáhněte autorizační token.

```html  
<body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
  <div id="content" style="display:none">
    <table width="100%">
      <tr>
        <td></td>
        <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
      </tr>
      <tr>
        <td align="right"><a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
        <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
      </tr>
      <tr>
        <td align="right">Region</td>
        <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
      </tr>
      <tr>
        <td align="right">Application ID:</td>
        <td><input id="appId" type="text" size="60" value="YOUR_LANGUAGE_UNDERSTANDING_APP_ID"></td>
      </tr>
      <tr>
        <td></td>
        <td><button id="startIntentRecognizeAsyncButton">Start Intent Recognition</button></td>
      </tr>
      <tr>
        <td align="right" valign="top">Input Text</td>
        <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
      </tr>
      <tr>
        <td align="right" valign="top">Result</td>
        <td><textarea id="statusDiv" style="display: inline-block;width:500px;height:100px"></textarea></td>
      </tr>
    </table>
  </div>

  <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>

  <script>
  // Note: Replace the URL with a valid endpoint to retrieve
  //       authorization tokens for your subscription.
  var authorizationEndpoint = "token.php";

  function RequestAuthorizationToken() {
    if (authorizationEndpoint) {
      var a = new XMLHttpRequest();
      a.open("GET", authorizationEndpoint);
      a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      a.send("");
      a.onload = function() {
                var token = JSON.parse(atob(this.responseText.split(".")[1]));
                serviceRegion.value = token.region;
                authorizationToken = this.responseText;
                subscriptionKey.disabled = true;
                subscriptionKey.value = "using authorization token (hit F5 to refresh)";
                console.log("Got an authorization token: " + token);
      }
    }
  }
  </script>

  <script>
    // status fields and start button in UI
    var phraseDiv;
    var statusDiv;
    var startIntentRecognizeAsyncButton;

    // subscription key, region, and appId for LUIS services.
    var subscriptionKey, serviceRegion, appId;
    var authorizationToken;
    var SpeechSDK;
    var recognizer;

    document.addEventListener("DOMContentLoaded", function () {
      startIntentRecognizeAsyncButton = document.getElementById("startIntentRecognizeAsyncButton");
      subscriptionKey = document.getElementById("subscriptionKey");
      serviceRegion = document.getElementById("serviceRegion");
      appId = document.getElementById("appId");
      phraseDiv = document.getElementById("phraseDiv");
      statusDiv = document.getElementById("statusDiv");

      startIntentRecognizeAsyncButton.addEventListener("click", function () {
        startIntentRecognizeAsyncButton.disabled = true;
        phraseDiv.innerHTML = "";
        statusDiv.innerHTML = "";
      });

      if (!!window.SpeechSDK) {
        SpeechSDK = window.SpeechSDK;
        startIntentRecognizeAsyncButton.disabled = false;

        document.getElementById('content').style.display = 'block';
        document.getElementById('warning').style.display = 'none';

        // in case we have a function for getting an authorization token, call it.
        if (typeof RequestAuthorizationToken === "function") {
          RequestAuthorizationToken();
        }
      }
    });
  </script>
```
 
## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Než budete moct inicializovat `SpeechRecognizer` objekt, musíte vytvořit konfiguraci, která používá váš klíč předplatného a oblast předplatného. Vložte tento kód do `startRecognizeOnceAsyncButton.addEventListener()` metody.

> [!NOTE]
> Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .


```JavaScript
        // if we got an authorization token, use the token. Otherwise use the provided subscription key
        var speechConfig;
        if (authorizationToken) {
          speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
        } else {
          if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
            alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
            return;
          }
          startIntentRecognizeAsyncButton.disabled = false;
          speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
        }

        speechConfig.speechRecognitionLanguage = "en-US";
```

## <a name="create-an-audio-configuration"></a>Vytvoření konfigurace zvuku

Nyní je třeba vytvořit `AudioConfig` objekt, který odkazuje na vstupní zařízení. Vložte tento kód do `startIntentRecognizeAsyncButton.addEventListener()` metody hned pod konfigurací řeči.

```JavaScript
        var audioConfig = SpeechSDK.AudioConfig.fromDefaultMicrophoneInput();
```

## <a name="initialize-a-intentrecognizer"></a>Inicializovat IntentRecognizer

Nyní vytvoříme `IntentRecognizer` objekt pomocí `SpeechConfig` `AudioConfig` dříve vytvořených objektů a. Vložte tento kód do `startIntentRecognizeAsyncButton.addEventListener()` metody.

```JavaScript
        recognizer = new SpeechSDK.IntentRecognizer(speechConfig, audioConfig);
```

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Přidat LanguageUnderstandingModel a záměry

Je potřeba přidružit k `LanguageUnderstandingModel` nástroji pro rozpoznávání záměrů a přidat záměry, které chcete rozpoznat. Budeme používat záměry z předem připravené domény pro automatizaci domů.

Vložte tento kód pod `IntentRecognizer` . Ujistěte se, že jste nahradili `"YourLanguageUnderstandingAppId"` ID aplikace Luis. 

```JavaScript
        if (appId.value !== "" && appId.value !== "YOUR_LANGUAGE_UNDERSTANDING_APP_ID") {
          var lm = SpeechSDK.LanguageUnderstandingModel.fromAppId(appId.value);

          recognizer.addAllIntents(lm);
        }
```

> [!NOTE]
> LanguageUnderstandingModel můžete vytvořit předáním adresy URL koncového bodu do metody FromEndpoint.
> Sada Speech SDK podporuje pouze koncové body LUIS v 2.0 a koncové body LUIS v 2.0 se vždy řídí jedním z těchto dvou vzorů:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Rozpoznávání záměru

Z `IntentRecognizer` objektu budete volat `recognizeOnceAsync()` metodu. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že po identifikaci fráze zastavit rozpoznávání řeči.

Vložte tento kód pod přidání modelu:

```JavaScript
        recognizer.recognizeOnceAsync(
          function (result) {
            window.console.log(result);
  
            phraseDiv.innerHTML = result.text + "\r\n";
  
            statusDiv.innerHTML += "(continuation) Reason: " + SpeechSDK.ResultReason[result.reason];
            switch (result.reason) {
              case SpeechSDK.ResultReason.RecognizedSpeech:
                statusDiv.innerHTML += " Text: " + result.text;
                break;
              case SpeechSDK.ResultReason.RecognizedIntent:
                statusDiv.innerHTML += " Text: " + result.text + " IntentId: " + result.intentId;
                
                // The actual JSON returned from Language Understanding is a bit more complex to get to, but it is available for things like
                // the entity name and type if part of the intent.
                statusDiv.innerHTML += " Intent JSON: " + result.properties.getProperty(SpeechSDK.PropertyId.LanguageUnderstandingServiceResponse_JsonResult);
                phraseDiv.innerHTML += result.properties.getProperty(SpeechSDK.PropertyId.LanguageUnderstandingServiceResponse_JsonResult) + "\r\n";
                break;
              case SpeechSDK.ResultReason.NoMatch:
                var noMatchDetail = SpeechSDK.NoMatchDetails.fromResult(result);
                statusDiv.innerHTML += " NoMatchReason: " + SpeechSDK.NoMatchReason[noMatchDetail.reason];
                break;
              case SpeechSDK.ResultReason.Canceled:
                var cancelDetails = SpeechSDK.CancellationDetails.fromResult(result);
                statusDiv.innerHTML += " CancellationReason: " + SpeechSDK.CancellationReason[cancelDetails.reason];
              
              if (cancelDetails.reason === SpeechSDK.CancellationReason.Error) {
                statusDiv.innerHTML += ": " + cancelDetails.errorDetails;
              }
            break;
            }
            statusDiv.innerHTML += "\r\n";
            startIntentRecognizeAsyncButton.disabled = false;
          },
          function (err) {
            window.console.log(err);
    
            phraseDiv.innerHTML += "ERROR: " + err;
            startIntentRecognizeAsyncButton.disabled = false;
          });
```

## <a name="check-your-code"></a>Kontrolovat kód

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/index-intent-recognition.html)]

## <a name="create-the-token-source-optional"></a>Vytvoření zdroje tokenu (volitelné)

Pokud chcete webovou stránku hostovat na webovém serveru, můžete pro ukázkovou aplikaci volitelně zadat zdroj tokenu.
Díky tomu váš klíč předplatného nikdy neopustí váš server a uživatelé budou moct využívat řečové funkce, aniž by sami museli zadávat jakýkoli autorizační kód.

Vytvořte nový soubor s názvem `token.php`. V tomto příkladu předpokládáme, že váš webový server podporuje skriptovací jazyk PHP s povoleným kudrlinkou. Zadejte následující kód:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Autorizační tokeny mají pouze omezenou dobu života.
> Tento zjednodušený příklad neukazuje, jak autorizační tokeny automaticky aktualizovat. Jako uživatel můžete stránku ručně znovu načíst nebo aktualizovat stisknutím klávesy F5.

## <a name="build-and-run-the-sample-locally"></a>Místní sestavení a spuštění ukázky

Pokud chcete aplikaci spustit, dvakrát klikněte na soubor index.html nebo tento soubor otevřete v oblíbeném webovém prohlížeči. Zobrazí se jednoduché grafické rozhraní (GUI), které vám umožní zadat klíč LUIS, [oblast Luis](../../../../regions.md)a ID aplikace Luis. Po zadání těchto polí můžete kliknutím na příslušné tlačítko aktivovat rozpoznávání pomocí mikrofonu.

> [!NOTE]
> Tato metoda nefunguje v prohlížeči Safari.
> V prohlížeči Safari musí být Ukázková webová stránka hostovaná na webovém serveru. Safari nepovoluje webům načtených z místního souboru, aby používaly mikrofon.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Sestavení a spuštění ukázky pomocí webového serveru

Pokud chcete aplikaci spustit, otevřete oblíbený webový prohlížeč a najeďte na něj veřejnou adresu URL, na které hostuje složku, zadejte svou [Luis oblast](../../../../regions.md) a také ID aplikace Luis a aktivujte rozpoznávání pomocí mikrofonu. Pokud se nakonfiguruje, získá token ze zdroje tokenu a začne rozpoznávat mluvené příkazy.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](footer.md)]
