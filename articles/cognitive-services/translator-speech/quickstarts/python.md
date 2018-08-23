---
title: Rychlý start Pythonu pro Azure Cognitive Services, Microsoft Translator Speech API | Dokumentace Microsoftu
description: Získat informace a ukázky kódu můžete rychle začít používat Microsoft Translator Speech API ve službě Microsoft Cognitive Services v Azure.
services: cognitive-services
documentationcenter: ''
author: v-jaswel
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jaswel
ms.openlocfilehash: a24ccd456df533542e7cbd2315fc1050cd82afbf
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "41987958"
---
# <a name="quickstart-for-microsoft-translator-speech-api-with-python"></a>Rychlý start pro Microsoft Translator Speech API s využitím Pythonu 
<a name="HOLTop"></a>

V tomto článku se dozvíte, jak používat Microsoft Translator Speech API pro převod slova v souboru ve formátu WAV.

## <a name="prerequisites"></a>Požadavky

Budete potřebovat [Python 3.x](https://www.python.org/downloads/) tento kód spustit.

Budete muset nainstalovat [klient technologie websocket balíčku](https://pypi.python.org/pypi/websocket-client) pro Python.

Budete potřebovat soubor WAV s názvem "speak.wav" ve stejné složce jako spustitelný soubor, který kompilaci z níže uvedeného kódu. Tento soubor ve formátu WAV musí být ve standardní PCM, 16 bitů, 16kHz, mono formátu. 

Musíte mít [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **Microsoft Translator Speech API**. Budete potřebovat placené předplatné klíče z vašeho [řídicí panel Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Překlad řeči

Následující kód přeloží řeči z jednoho jazyka do druhého.

1. Vytvoření nového projektu Pythonu v oblíbeném prostředí IDE.
2. Přidejte níže uvedený kód.
3. Nahraďte `key` hodnotu s přístupový klíč platný pro vaše předplatné.
4. Spuštění programu.

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

**Překlad řeči odpovědi**

Úspěšný výsledek je vytvoření souboru s názvem "speak2.wav". Tento soubor obsahuje překlad slova, kterým se mluví ve "speak.wav".

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Translator Speech kurz](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled rozhraní Translator Speech](../overview.md)
[Reference k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
