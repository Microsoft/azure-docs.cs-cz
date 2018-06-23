---
title: Node.js rychlý úvodní kurz pro Azure kognitivní služby Microsoft překladač řeči rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API pro rozpoznávání řeči překladač Microsoft v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: d469fa008ba8acaf505fa09596dd739d5cc7744c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342661"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-nodejs"></a>Rychlý start pro Microsoft překladač řeči rozhraní API pomocí Node.js 
<a name="HOLTop"></a>

Tento článek ukazuje, jak používat rozhraní API pro rozpoznávání řeči překladač Microsoft překládat slova používaný v souboru ve formátu WAV.

## <a name="prerequisites"></a>Požadavky

Je třeba [Node.js 6](https://nodejs.org/en/download/) pro spuštění tohoto kódu.

Je potřeba nainstalovat [Websocket balíček](https://www.npmjs.com/package/websocket) pro Node.js.

Je třeba soubor ve formátu WAV s názvem "speak.wav" ve stejné složce jako spustitelný soubor, které zkompilujete z následující kód. Tento soubor ve formátu WAV musí být ve standardní PCM, 16 bitů, 16 kHz mono formátu. Můžete získat takové .wav soubor z [překladač Text řeči API](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak).

Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API služby Microsoft překladač řeči**. Potřebujete placené předplatné klíč z vaší [řídicí panel Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Převede řeči

Následující kód překládá řeči z jednoho jazyka do druhého.

1. Vytvořte nový projekt Node.js v vaše oblíbená rozhraní IDE.
2. Přidejte kód níže uvedenou.
3. Nahraďte `key` hodnotu s přístupový klíč platný pro vaše předplatné.
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
You can obtain such a .wav file using the Translator Text Speak API. See:
http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak
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
This lets the service know that the audio input is finished. */
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

**Převede řeči odpovědi**

Úspěšné výsledkem je vytvoření souboru s názvem "speak2.wav". Tento soubor obsahuje překlad slova používaný v "speak.wav".

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Překladač řeči kurzu](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled řeči překladač](../overview.md)
[referenční dokumentace rozhraní API](http://docs.microsofttranslator.com/speech-translate.html)
