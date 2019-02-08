---
title: Migrace z Translator Speech API k Speech Service
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak migrovat aplikace z rozhraní Translator Speech API ve službě řeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: de9727df9255fb880403e0409055b73db240e882
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868105"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrace z Translator Speech API k Speech Service

V tomto článku použijte k migraci aplikace z Microsoft Translator Speech API k [Speech Service](index.yml). Tato příručka popisuje rozdíly mezi rozhraní Translator Speech API a Speech Service a navrhne strategie pro migraci aplikací.

> [!NOTE]
> Váš klíč předplatného rozhraní Translator Speech API nebude změna přijata službou řeči. Je potřeba spustit nový odběr Speech Service.

## <a name="comparison-of-features"></a>Porovnání funkcí

| Funkce                                           | Translator Speech API                                  | Speech Service | Podrobnosti                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Překlad textu                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Překlad řeči                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globální koncový bod                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Speech Service aktuálně nenabízí globální koncový bod. Globální koncový bod může automaticky směrovat provoz na nejbližší regionální koncový bod, snížení latence ve vaší aplikaci.                                                    |
| Místní koncové body                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Časový limit připojení                             | 90 minut                                               | Neomezený počet pomocí sady SDK. 10 minut s připojením Websocket.                                                                                                                                                                                                                                                                                   |
| Klíč ověření v záhlaví                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Přeložit v jedné žádosti více jazyků | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Sady SDK k dispozici                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Zobrazit [Speech Service dokumentaci](index.yml) pro dostupné sady SDK.                                                                                                                                                    |
| Protokoly Websocket připojení                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Jazyky rozhraní API                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Speech Service podporuje stejný rozsah jazyky podle [reference k rozhraní Translator API jazyků](../translator-speech/languages-reference.md) článku. |
| Filtr vulgárních výrazů a značky                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM jako vstup                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Jiné typy souborů jako vstup                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Částečné výsledky.                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informace o časování                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| ID korelace                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modely řeči                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Speech Service nabízí modely řeči, které vám umožní přizpůsobit rozpoznávání řeči do slovníku vaší organizace jedinečný.                                                                                                                                           |
| Vlastní překladové modely                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Přihlášení k odběru do rozhraní API pro překlad textu Microsoftu vám umožní použít [vlastní Translator](https://www.microsoft.com/translator/business/customization/) moct používat vaše vlastní data pro přesnější překlady.                                                 |

## <a name="migration-strategies"></a>Strategie migrace

Pokud aplikace v vývojové nebo produkční prostředí, které používají rozhraní Translator Speech API máte vy nebo vaše organizace, měli byste aktualizovat je, aby používaly službu rozpoznávání řeči. Zobrazit [Speech Service](index.yml) dokumentaci dostupné sady SDK, ukázky a kurzy. Při migraci, zvažte následující:

* Speech Service aktuálně nenabízí globální koncový bod. Určete, pokud funkce aplikace efektivně pokud používá jeden místní koncový bod pro všechny jeho provozu. Pokud ne, použijte informace o zeměpisné poloze určit nejefektivnější koncový bod.

* Pokud vaše aplikace používá s dlouhým poločasem rozpadu připojení a nemůžete použít dostupné sady SDK, můžete použít objekty Websocket připojení. Spravujte během 10 minut časový limit připojení ve vhodných chvílích.

* Pokud vaše aplikace používá rozhraní Translator Text API a rozhraní Translator Speech API umožňuje překlad vlastní modely, můžete přidat ID kategorie přímo pomocí Speech Service.

* Na rozdíl od rozhraní Translator Speech API Speech Service můžete dokončit překlady do více jazyků v jedné žádosti.

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte zdarma službu rozpoznávání řeči](get-started.md)
* [Rychlé zprovoznění: Rozpoznávání řeči do aplikace pro UPW pomocí sady SDK pro řeč](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Speech Service](overview.md)
* [Dokumentace k Speech Service a sady SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
