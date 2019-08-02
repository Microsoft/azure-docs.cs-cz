---
title: Migrace z Translator Speech API do služby pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se migrovat aplikace z Translator Speech API do služby pro rozpoznávání řeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 7b61aef13b113d9b2502c24e3001da25fa186c76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559562"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrace z Translator Speech API do služby pro rozpoznávání řeči

Pomocí tohoto článku migrujete své aplikace z Microsoft Translator Speech API do [služby Speech](index.yml). Tato příručka popisuje rozdíly mezi Translator Speech API a službu Speech Service a navrhuje strategie pro migraci vašich aplikací.

> [!NOTE]
> Služba rozpoznávání řeči nepřijímá váš klíč předplatného Translator Speech API. Budete muset vytvořit nové předplatné služby Speech Services.

## <a name="comparison-of-features"></a>Porovnání funkcí

| Funkce                                           | Translator Speech API                                  | Hlasové služby | Podrobnosti                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Převod na text                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Převod na řeč                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globální koncový bod                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Služba Speech Services nenabízí globální koncový bod. Globální koncový bod může automaticky směrovat provoz na nejbližší oblastní koncový bod a snížit latenci ve vaší aplikaci.                                                    |
| Regionální koncové body                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Časový limit připojení                             | 90 minut                                               | Neomezeno pomocí sady SDK. 10 minut s připojením typu WebSockets.                                                                                                                                                                                                                                                                                   |
| Ověřovací klíč v záhlaví                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Více jazyků, které jsou přeloženy v jednom požadavku | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Dostupné sady SDK                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Dostupné sady SDK najdete v [dokumentaci ke službám Speech](index.yml) .                                                                                                                                                    |
| Připojení k objektům WebSocket                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Rozhraní API jazyků                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Služba Speech Services podporuje stejný rozsah jazyků popsaných v článku [referenční informace o jazycích rozhraní Translator API](../translator-speech/languages-reference.md) . |
| Filtr a značka vulgárních výrazů                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM jako vstup                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Jiné typy souborů jako vstup                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Částečné výsledky                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informace o časování                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| ID korelace                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Vlastní modely řeči                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Služba Speech Services nabízí vlastní modely řeči, které umožňují přizpůsobit rozpoznávání řeči unikátnímu slovníku vaší organizace.                                                                                                                                           |
| Vlastní modely překladu                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Přihlášení k odběru rozhraní API pro překlad textu do Microsoftu umožňuje [](https://www.microsoft.com/translator/business/customization/) používat vlastní překladatele k používání vlastních dat pro přesnější překlady.                                                 |

## <a name="migration-strategies"></a>Strategie migrace

Pokud vy nebo vaše organizace máte aplikace ve vývojovém nebo produkčním prostředí, které používají Translator Speech API, měli byste je aktualizovat, aby používaly službu Speech. Dostupné sady SDK, ukázky kódu a kurzy najdete v dokumentaci ke [službě Speech](index.yml) . Při migraci Vezměte v úvahu následující:

* Služba Speech Services nenabízí globální koncový bod. Zjistěte, jestli vaše aplikace funguje efektivně, když používá jeden místní koncový bod pro veškerý provoz. Pokud ne, použijte geografickou polohu k určení nejefektivnějšího koncového bodu.

* Pokud vaše aplikace používá dlouhodobá připojení a nemůže využívat dostupné sady SDK, můžete použít připojení pomocí protokolu WebSockets. Nastavte časový limit 10 minut tím, že se znovu připojíte k odpovídajícím časem.

* Pokud vaše aplikace používá Translator Text API a Translator Speech API k povolení vlastních modelů překladu, můžete přidat ID kategorií přímo pomocí služby Speech.

* Na rozdíl od Translator Speech API můžou služby Speech doplňovat překlady do několika jazyků v jednom požadavku.

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte si hlasové služby zdarma](get-started.md)
* [Rychlé zprovoznění: Rozpoznávání řeči v aplikaci pro UWP pomocí sady Speech SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Viz také:

* [Co je Speech Service](overview.md)
* [Dokumentace ke službám Speech a sadě Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
