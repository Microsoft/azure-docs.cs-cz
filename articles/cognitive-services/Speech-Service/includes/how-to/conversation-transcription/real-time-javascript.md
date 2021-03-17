---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 502729e4c90a6d6ffa424ea7f379e2b426b3e3e0
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445269"
---
## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Předtím, než můžete cokoli udělat, musíte nainstalovat <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">sadu Speech SDK pro JavaScript </a>. V závislosti na vaší platformě postupujte podle následujících pokynů:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webový prohlížeč </a>

## <a name="create-voice-signatures"></a>Vytvoření podpisů hlasu

Prvním krokem je vytvoření hlasových podpisů pro účastníky konverzace, aby je bylo možné identifikovat jako jedinečné mluvčí. Vstupní `.wav` zvukový soubor pro tvorbu hlasových podpisů by měl být 16bitový, 16 kHz vzorkování a formát jednoho kanálu (mono). Doporučená délka každého zvukového vzorku je mezi třiceti sekund a dvě minuty. `.wav`Soubor by měl být ukázkou hlasu **jednoho uživatele** , aby se vytvořil jedinečný hlasový profil.

Následující příklad ukazuje, jak vytvořit hlasový podpis [pomocí REST API](https://aka.ms/cts/signaturegenservice) v JavaScriptu. Všimněte si, že je potřeba nahradit skutečné informace pro `subscriptionKey` , `region` a cestu k ukázkovému `.wav` souboru.

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

Spuštění tohoto skriptu vrátí řetězec hlasového podpisu v proměnné `voiceSignatureString` . Spusťte funkci dvakrát, abyste měli dva řetězce pro použití jako vstup do proměnných `voiceSignatureStringUser1` a `voiceSignatureStringUser2` níže.

> [!NOTE]
> Signatury hlasu se dají vytvořit **jenom** pomocí REST API.

## <a name="transcribe-conversations"></a>Konverzace přepisovat

Následující vzorový kód ukazuje, jak přepisovat konverzace v reálném čase pro dva reproduktory. Předpokládá se, že jste už vytvořili řetězce hlasových podpisů pro každého mluvčího, jak vidíte výše. Nahraďte reálné informace pro `subscriptionKey` , `region` a cestu `filepath` pro zvuk, který chcete přepisovat.

Tento vzorový kód provede následující:

* Vytvoří datový proud push, který se použije pro přepis, a zapíše `.wav` do něj ukázkový soubor.
* Vytvoří `Conversation` pomocí `createConversationAsync()` .
* Vytvoří `ConversationTranscriber` pomocí konstruktoru.
* Přidá účastníky do konverzace. Řetězce `voiceSignatureStringUser1` , které `voiceSignatureStringUser2` by měly být zadány jako výstup z výše uvedeného postupu.
* Zaregistruje se na události a začne přepis.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```