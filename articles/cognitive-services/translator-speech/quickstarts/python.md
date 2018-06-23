---
title: Python rychlý úvodní kurz pro Azure kognitivní služby Microsoft překladač řeči rozhraní API | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat rozhraní API pro rozpoznávání řeči překladač Microsoft v kognitivní služby společnosti Microsoft na platformě Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jaswel
ms.openlocfilehash: 29722b3fa7fe61aff0b2406002453020d999ea41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342634"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-python"></a>Rychlý start pro Microsoft překladač řeči rozhraní API s Pythonem 
<a name="HOLTop"></a>

Tento článek ukazuje, jak používat rozhraní API pro rozpoznávání řeči překladač Microsoft překládat slova používaný v souboru ve formátu WAV.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat [Python 3.x](https://www.python.org/downloads/) pro spuštění tohoto kódu.

Budete muset nainstalovat [balíček klienta protokolu websocket](https://pypi.python.org/pypi/websocket-client) pro jazyk Python.

Budete potřebovat soubor ve formátu WAV s názvem "speak.wav" ve stejné složce jako spustitelný soubor, které zkompilujete z následující kód. Tento soubor ve formátu WAV musí být ve standardní PCM, 16 bitů, 16kHz mono formátu. Můžete získat takové .wav soubor z [překladač Text řeči API](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Speak).

Musíte mít [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API služby Microsoft překladač řeči**. Budete potřebovat předplatné klíč z vaší [řídicí panel Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Převede řeči

Následující kód překládá řeči z jednoho jazyka do druhého.

1. Vytvořte nový projekt Python v vaše oblíbená rozhraní IDE.
2. Přidejte kód níže uvedenou.
3. Nahraďte `key` hodnotu s přístupový klíč platný pro vaše předplatné.
4. Spusťte program.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate';
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray ()

def on_open (client):
    print ("Connected.")

# r = read. b = binary.
    with open (input_file, mode='rb') as file:
        data = file.read()

    print ("Sending audio.")
    client.send (data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print ("Sending silence.")
    client.send (bytearray (32000), websocket.ABNF.OPCODE_BINARY)

def on_data (client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print ("Received text data.")
        print (message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print ("Received binary data.")
        print ("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
# w = write. b = binary.
            with open (output_file, mode='wb') as file:
                file.write (output)
                print ("Wrote data to output file.")
            client.close ()
    else:
        print ("Received data of type: " + str (message_type))

def on_error (client, error):
    print ("Connection error: " + str (error))

def on_close (client):
    print ("Connection closed.")

client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print ("Connecting...")
client.run_forever()
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
