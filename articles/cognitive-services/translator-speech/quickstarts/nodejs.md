---
title: Rychlý úvod k Node.js pro Azure Cognitive Services, Microsoft Translator Speech API | Dokumentace Microsoftu
description: Získat informace a ukázky kódu můžete rychle začít používat Microsoft Translator Speech API ve službě Microsoft Cognitive Services v Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: e652ee1e201e60d0d75f10bb8fceb4fbcd205381
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "41987463"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-nodejs"></a>Rychlý start pro Microsoft Translator Speech API s využitím Node.js 
<a name="HOLTop"></a>

V tomto článku se dozvíte, jak používat Microsoft Translator Speech API pro převod slova v souboru ve formátu WAV.

## <a name="prerequisites"></a>Požadavky

Potřebujete [Node.js 6](https://nodejs.org/en/download/) tento kód spustit.

Je potřeba nainstalovat [balíček pomocí protokolu Websocket](https://www.npmjs.com/package/websocket) pro Node.js.

Budete potřebovat soubor ve formátu WAV s názvem "speak.wav" ve stejné složce jako spustitelný soubor, který kompilaci z níže uvedeného kódu. Tento soubor ve formátu WAV musí být ve standardní PCM, 16 bitů, 16 kHz, mono formátu. Můžete získat takový WAV soubor z [převod textu na řeč API](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **Microsoft Translator Speech API**. Budete potřebovat placené předplatné klíče z vašeho [řídicí panel Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Překlad řeči

Následující kód přeloží řeči z jednoho jazyka do druhého.

1. Vytvořte nový projekt Node.js v oblíbeném prostředí IDE.
2. Přidejte níže uvedený kód.
3. Nahraďte `key` hodnotu s přístupový klíč platný pro vaše předplatné.
4. Spuštění programu.

```nodejs
/* To install this dependency, run:
npm install websocket
*/
var wsClient = require('websocket').client;
var fs = require('fs');
/* To install this dependency, run:
npm install stream-buffers
*/
var streamBuffers = require('stream-buffers');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let key = 'ENTER KEY HERE';

let host = 'wss://dev.microsofttranslator.com';
let path = '/speech/translate';
let params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa';
let uri = host + path + params;

/* The input .wav file is in PCM 16bit, 16kHz, mono format.
You can obtain such a .wav file using the Text to Speech API. See:
https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech
*/
let input_path = 'speak.wav';

let output_path = 'speak2.wav';

function receive(message) {
    console.log ("Received message of type: " + message.type + ".");

    if (message.type == 'utf8') {
        var result = JSON.parse(message.utf8Data)
        console.log("Response type: " + result.type + ".");
        console.log("Recognized input as: " + result.recognition);
        console.log("Translation: " + result.translation);
    }
    else if (message.type == 'binary') {
/* This is needed to make sure message.binaryData is defined. See:
https://stackoverflow.com/questions/17546953/cant-access-object-property-even-though-it-exists-returns-undefined
*/
        let binary_data = JSON.parse (JSON.stringify (message.binaryData));

        if (binary_data.type == 'Buffer') {
/* Put the binary data in a Buffer - do not write it directly to the file. */
            let buffer = new Buffer(binary_data.data, 'binary');
/* Write the contents of the Buffer to the file. */
            fs.writeFile (output_path, buffer, 'binary', function (error) {
/* fs.writeFile calls the error-handling function even if there is no error, in which case error === null. See:
https://stackoverflow.com/questions/44473868/node-js-fs-writefile-err-returns-null
*/
                if (error !== null) {
                    console.log ("Error: " + error);
                }
            });
        }
    }
}

function send(connection, filename) {
    
    var myReadableStreamBuffer = new streamBuffers.ReadableStreamBuffer({
        frequency: 100,
        chunkSize: 32000
    });

/* Make sure the audio file is followed by silence.
This lets the service know that the audio file is finished.
At 32 bytes per millisecond, this is 100 seconds of silence. */
    myReadableStreamBuffer.put(fs.readFileSync(filename));
    myReadableStreamBuffer.put(new Buffer(3200000));
    myReadableStreamBuffer.stop();

    myReadableStreamBuffer.on('data', function (data) {
        connection.sendBytes(data);
    });

    myReadableStreamBuffer.on('end', function () {
        console.log('Closing connection.');
        connection.close(1000);
    });
}

function connect() {
    var ws = new wsClient();

    ws.on('connectFailed', function (error) {
        console.log('Connection error: ' + error.toString());
    });
                            
    ws.on('connect', function (connection) {
        console.log('Connected.');

        connection.on('message', receive);
        
        connection.on('close', function (reasonCode, description) {
            console.log('Connection closed: ' + reasonCode);
        });

        connection.on('error', function (error) {
            console.log('Connection error: ' + error.toString());
        });
        
        send(connection, input_path);
    });

    ws.connect(uri, null, null, { 'Ocp-Apim-Subscription-Key' : key });
}

connect();
```

**Překlad řeči odpovědi**

Úspěšný výsledek je vytvoření souboru s názvem "speak2.wav". Tento soubor obsahuje překlad slova, kterým se mluví ve "speak.wav".

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Translator Speech kurz](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled rozhraní Translator Speech](../overview.md)
[Reference k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
