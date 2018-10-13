---
title: 'Rychlý start: Rozhraní Translator Speech API v Pythonu'
titlesuffix: Azure Cognitive Services
description: Získejte informace a ukázky kódu, které vám pomůžou rychle začít používat rozhraní Translator Speech API.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: v-jaswel
ROBOTS: NOINDEX
ms.openlocfilehash: 601e8473482cf6d90151b221309aef4d48d368d0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979011"
---
# <a name="quickstart-translator-speech-api-with-python"></a>Rychlý start: Rozhraní Translator Speech API v Pythonu
<a name="HOLTop"></a>

V tomto článku se dozvíte, jak použít rozhraní Translator Speech API k překladu vyslovených slov do souboru .wav.

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, budete potřebovat [Python 3.x](https://www.python.org/downloads/).

Budete muset nainstalovat [balíček websocket-client](https://pypi.python.org/pypi/websocket-client) pro Python.

Budete potřebovat soubor .wav s názvem „speak.wav“ ve stejné složce jako spustitelný soubor, který zkompilujete z níže uvedeného kódu. Tento soubor .wav musí být ve standardním 16bitovém, 16kHz monofonním formátu PCM. 

Musíte mít [účet rozhraní Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s rozhraním **Microsoft Translator Speech API**. Budete potřebovat placené klíče předplatného ze svého [řídicího panelu Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Překlad řeči

Následující kód překládá řeč z jednoho jazyka do druhého.

1. V oblíbeném integrovaném vývojovém prostředí (IDE) vytvořte nový projekt Pythonu.
2. Přidejte níže uvedený kód.
3. Hodnotu `key` nahraďte přístupovým klíčem platným pro vaše předplatné.
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

**Odpověď s přeloženou řečí**

Úspěšným výsledkem je vytvoření souboru s názvem „speak2.wav“. Tento soubor obsahuje překlad vyslovených slov ze souboru „speak.wav“.

[Zpět na začátek](#HOLTop)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz ke službě Translator Speech](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Viz také 

[Přehled služby Translator Speech](../overview.md)
[Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
