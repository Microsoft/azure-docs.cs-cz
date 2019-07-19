---
title: 'Rychlý start: Analýza textového obsahu v Pythonu – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Jak analyzovat textový obsah pro různé nežádoucím pomocí sady obsahu Moderator SDK pro Python
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 01c153f2f8836b7d99de57af60b8623e54c6d6fe
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311929"
---
[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Rychlý start: Analýza obsahu textu pro nevhodný materiál v Pythonu

Tento článek obsahuje informace a ukázky kódu, které vám pomůžou začít používat Content Moderator SDK pro Python. Dozvíte se, jak provádět filtrování založené na termínech a klasifikaci obsahu textu za účelem moderování potenciálně problematického materiálu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky
- Klíč předplatného Content Moderatoru. Podle pokynů v tématu [Vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) se přihlaste k odběru Content Moderatoru a získejte svůj klíč.
- [Python 2.7+ nebo 3.5+](https://www.python.org/downloads/)
- Nástroj [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-content-moderator-sdk"></a>Získat Content Moderator SDK

Nainstalujte SDK pro Python Content Moderator otevřením příkazového řádku a spuštěním následujícího příkazu:

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Import modulů

Vytvořit nový skript Pythonu s názvem _ContentModeratorQS.py_ a přidejte následující kód, který importuje nezbytné součásti sady SDK. K dispozici je modul tisku s nejjednodušším tiskem, který je snazší číst odpověď JSON.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>Inicializace proměnných

V dalším kroku přidejte proměnné pro vaše Content Moderator předplatné key a koncového bodu adresy URL. Do proměnných prostředí budete muset přidat název `CONTENT_MODERATOR_SUBSCRIPTION_KEY` a jako jeho hodnotu přidat klíč předplatného. V případě adresy URL základního koncového `CONTENT_MODERATOR_ENDPOINT` bodu přidejte do proměnných prostředí s adresou URL konkrétní oblasti jako jeho hodnotu, `https://westus.api.cognitive.microsoft.com`například. Bezplatné předplatné zkušební verze klíče jsou generovány v **westus** oblasti.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

Řetězec víceřádkového textu ze souboru bude moderovaný. Do místní kořenové složky zahrňte soubor [content_moderator_text_moderation. txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) a přidejte jeho název souboru do proměnných:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Dotazování na službu moderátora

Vytvoření **ContentModeratorClient** instance pomocí vaše předplatné key a koncového bodu adresy URL. 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

Pak použijte svého klienta s jeho členskou instancí **TextModerationOperations** k volání rozhraní API pro moderování s funkcí `screen_text`. Zobrazit **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** referenční dokumentaci pro další informace o tom, jak ji volat.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>Zkontroluje vytištěnou odpověď.

Spusťte ukázku a potvrďte odpověď. Mělo by být úspěšně dokončeno a vrácena instance **obrazovky** . Níže je vytištěn úspěšný výsledek:

Ukázkový text používaných pro tento rychlý start za následek následující výstup:

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili jednoduchý skript Pythonu, která používá službu Content Moderator vrátit relevantní informace o ukázku daného textu. Dále si můžete přečíst další informace o tom, co znamenají různé příznaky a klasifikace, abyste se mohli rozhodnout, jaká data potřebujete a jak by je vaše aplikace měla zpracovávat.

> [!div class="nextstepaction"]
> [Příručka moderování textu](text-moderation-api.md)
