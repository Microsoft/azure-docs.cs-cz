---
title: 'Rychlý start: Translator Speech API Node.js'
titlesuffix: Azure Cognitive Services
description: Získejte informace a ukázky kódu, které vám pomůžou rychle začít používat rozhraní Translator Speech API.
services: cognitive-services
author: v-jaswel
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: a76d3c3ed54b3b4d9d79cd778c75719dd12507d5
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874018"
---
# <a name="quickstart-translator-speech-api-with-nodejs"></a>Rychlý start: Translator Speech API s využitím Node.js 
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

V tomto článku se dozvíte, jak použít rozhraní Translator Speech API k překladu vyslovených slov do souboru .wav.

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, potřebujete [Node.js 6](https://nodejs.org/en/download/).

Je potřeba nainstalovat [balíček Websocket](https://www.npmjs.com/package/websocket) pro Node.js.

Potřebujete soubor .wav s názvem „speak.wav“ ve stejné složce jako spustitelný soubor, který zkompilujete z níže uvedeného kódu. Tento soubor .wav musí být ve standardním 16bitovém, 16kHz monofonním formátu PCM. Takový soubor .wav můžete získat z [rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Musíte mít [účet rozhraní Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraním **Microsoft Translator Speech API**. Potřebujete klíč placeného předplatného ze svého [řídicího panelu Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Překlad řeči

Následující kód překládá řeč z jednoho jazyka do druhého.

1. Ve svém oblíbeném integrovaném vývojovém prostředí vytvořte nový projekt Node.js.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
4. Spusťte program.

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
https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis
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

**Odpověď s přeloženou řečí**

Úspěšným výsledkem je vytvoření souboru s názvem „speak2.wav“. Tento soubor obsahuje překlad vyslovených slov ze souboru „speak.wav“.

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz ke službě Translator Speech](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled služby Translator Speech](../overview.md)
[Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
