---
title: Migrace z Translator Speech API k Speech Service
titleSuffix: Azure Cognitive Services
description: Toto téma použijte k migraci aplikace z rozhraní Translator Speech API ve službě řeči.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 10/01/2018
ms.author: aahi
ms.openlocfilehash: 55303b38a5fe8890cddc2336b4bc47a23fc03745
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239927"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrace z Translator Speech API k Speech Service

V tomto článku použijte k migraci aplikace z Microsoft Translator Speech API k [Speech Service](index.yml). Tato příručka popisuje rozdíly mezi rozhraní Translator Speech API a Speech Service a navrhne strategie pro migraci aplikací.

> [!NOTE]
> Váš klíč předplatného rozhraní Translator Speech API nebude změna přijata službou řeči. Je potřeba spustit nový odběr Speech Service.

## <a name="comparison-of-features"></a>Porovnání funkcí

| Funkce                                           | Translator Speech API                                  | Speech Service | Podrobnosti                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Překlad textu                               | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Překlad řeči                             | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globální koncový bod                                   | : heavy_check_mark:                                              | : heavy_minus_sign:                 | Speech Service aktuálně nenabízí globální koncový bod. Globální koncový bod může automaticky směrovat provoz na nejbližší regionální koncový bod, snížení latence ve vaší aplikaci.                                                    |
| Místní koncové body                                | : heavy_minus_sign:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Časový limit připojení                             | 90 minut                                               | Neomezený počet pomocí sady SDK. 10 minut s připojením websocket                                                                                                                                                                                                                                                                                   |
| Klíč ověření v záhlaví                                | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Přeložit v jedné žádosti více jazyků | : heavy_minus_sign:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Sady SDK k dispozici                                    | : heavy_minus_sign:                                              | : heavy_check_mark:                 | Zobrazit [Speech Service dokumentaci](index.yml) pro dostupné sady SDK.                                                                                                                                                    |
| Připojení pomocí protokolu Websocket                             | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Jazyky rozhraní API                                     | : heavy_check_mark:                                              | : heavy_minus_sign:                 | Speech Service podporuje stejný rozsah jazyky podle [reference k rozhraní Translator API jazyků](../translator-speech/languages-reference.md) článku. |
| Filtr vulgárních výrazů a značky                       | : heavy_minus_sign:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| . WAV/PCM jako vstup                                 | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Jiné typy souborů jako vstup                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Částečné výsledky.                                   | : heavy_check_mark:                                              | : heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informace o časování                                       | : heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| ID korelace                                    | : heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modely řeči                              | : heavy_minus_sign:                                              | : heavy_check_mark:                 | Speech Service nabízí modely řeči, které vám umožní přizpůsobit rozpoznávání řeči do slovníku vaší organizace jedinečný.                                                                                                                                           |
| Vlastní překladové modely                         | : heavy_minus_sign:                                              | : heavy_check_mark:                 | Přihlášení k odběru do rozhraní API pro překlad textu Microsoftu vám umožní použít [vlastní Translator](https://www.microsoft.com/translator/business/customization/) (aktuálně ve verzi preview) použít vlastní data pro přesnější překlady.                                                 |

## <a name="migration-strategies"></a>Strategie migrace

Pokud aplikace v vývojové nebo produkční prostředí, které používají rozhraní Translator Speech API máte vy nebo vaše organizace, měli byste aktualizovat je, aby používaly službu rozpoznávání řeči. Zobrazit [Speech Service](index.yml) dokumentaci dostupné sady SDK, ukázky a kurzy. V následující tabulce jsou některé věci k uvážení při migraci:

* Speech Service aktuálně nenabízí globální koncový bod. Je potřeba určit, pokud vaše aplikace bude fungovat efektivně pomocí jednoho koncového bodu místní pro všechny jeho provozu. Pokud to tak není, musíte použít informace o zeměpisné poloze k určení, co nejúčinnější koncový bod.

* Pokud vaše aplikace používá s dlouhým poločasem rozpadu připojení a nemůžete použít dostupné sady SDK, můžete použít připojení soketu websocket a spravovat během 10 minut časový limit o opětovné připojení ve vhodných chvílích.

* Pokud vaše aplikace používá rozhraní Translator Text API a rozhraní Translator Speech API umožňuje překlad vlastní modely, budete moct přidat přímo pomocí Speech Service ID "Kategorie".

* Speech Service můžete dokončit překlady do více jazyků v jedné žádosti, na rozdíl od rozhraní Translator Speech API.

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte zdarma službu rozpoznávání řeči](get-started.md)
* [Rychlý start: Rozpoznávat řeč v aplikace pro UPW pomocí sady SDK pro řeč](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Speech service](overview.md)
* [Dokumentace k Speech Service a sady SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg)
