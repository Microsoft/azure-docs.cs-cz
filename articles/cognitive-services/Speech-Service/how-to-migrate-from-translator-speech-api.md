---
title: Migrace z rozhraní API pro překladač řeči do služby Řeč
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak migrovat aplikace z rozhraní Translator Speech API do služby Řeč.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 75a456c4a297b0465c34b8e0af2e87056ad565b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560894"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrace z rozhraní API pro překladač řeči do služby Řeč

Tento článek slouží k migraci aplikací z rozhraní Microsoft Translator Speech API do [služby Řeč](index.yml). Tato příručka popisuje rozdíly mezi překladačem řeči API a řeči služby a navrhuje strategie pro migraci aplikací.

> [!NOTE]
> Váš klíč předplatného rozhraní API pro řeč překladače nebude službou Speech přijat. Budete muset vytvořit nové předplatné služby Speech.

## <a name="comparison-of-features"></a>Porovnání funkcí

| Funkce                                           | Translator Speech API                                  | Služba Speech | Podrobnosti                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Překlad na text                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Překlad do řeči                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globální koncový bod                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Služba Řeč nenabízí globální koncový bod. Globální koncový bod může automaticky směrovat provoz na nejbližší místní koncový bod, což snižuje latenci ve vaší aplikaci.                                                    |
| Místní koncové body                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Časový limit připojení                             | 90 minut                                               | Neomezené s SDK. 10 minut s připojením WebSockets.                                                                                                                                                                                                                                                                                   |
| Ověřovací klávesa v záhlaví                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Více jazyků přeložených v jedné žádosti | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| K dispozici sady SDK                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Dostupné sady SDK naleznete v [dokumentaci ke službě Řeč.](index.yml)                                                                                                                                                    |
| Připojení WebSockets                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Jazyky API                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Služba Řeč podporuje stejný rozsah jazyků popsaných v [článku reference překladače jazyků rozhraní API.](../translator-speech/languages-reference.md) |
| Filtr a značka vulgárních výrazů                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM jako vstup                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Jiné typy souborů jako vstup                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Částečné výsledky                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informace o čase                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID korelace                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Vlastní modely řeči                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Služba Řeč nabízí vlastní modely řeči, které umožňují přizpůsobit rozpoznávání řeči jedinečnému slovníku vaší organizace.                                                                                                                                           |
| Vlastní modely překladů                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Přihlášení k odběru rozhraní Microsoft Text Translation API umožňuje použít [vlastní překladač](https://www.microsoft.com/translator/business/customization/) používat vlastní data pro přesnější překlady.                                                 |

## <a name="migration-strategies"></a>Strategie migrace

Pokud vy nebo vaše organizace mají aplikace ve vývoji nebo produkčním prostředí, které používají překladač řeči rozhraní API, měli byste je aktualizovat tak, aby používat službu řeči. Informace o dostupných sadách SDK, ukázkách kódu a kurzech naleznete v dokumentaci [ke službě Řeč.](index.yml) Při migraci zvažte následující:

* Služba Řeč nenabízí globální koncový bod. Zjistěte, zda vaše aplikace funguje efektivně, když používá jeden místní koncový bod pro všechny jeho provoz. Pokud ne, použijte geolokaci k určení nejúčinnějšího koncového bodu.

* Pokud vaše aplikace používá dlouhotrvající připojení a nelze použít dostupné sady SDK, můžete použít připojení WebSockets. Spravujte limit 10minutového časového limitu opětovným připojením ve vhodnou dobu.

* Pokud vaše aplikace používá překladač text api a překladač řeči rozhraní API povolit vlastní modely překladu, můžete přidat ID kategorií přímo pomocí služby řeči.

* Na rozdíl od překladače řeči rozhraní API řeči služby řeči můžete dokončit překlady do více jazyků v jedné žádosti.

## <a name="next-steps"></a>Další kroky

* [Vyzkoušejte službu Speech zdarma](get-started.md)
* [Úvodní příručka: Rozpoznávání řeči v aplikaci UPW pomocí sady Speech SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Viz také

* [Co je služba Řeč](overview.md)
* [Dokumentace služby rozpoznávání řeči a sady SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
