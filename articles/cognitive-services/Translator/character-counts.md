---
title: Počty znaků – překladač textového rozhraní API
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak Azure Cognitive Services Translator Text API počítá znaky, takže můžete pochopit, jak ingestuje obsah.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888158"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak překladač text api počítá znaky

Překladač text rozhraní API spočítá každý bod kódu Unicode vstupní text jako znak. Každý překlad textu do jazyka se počítá jako samostatný překlad, i když byl požadavek proveden v jednom volání rozhraní API překládat do více jazyků. Na délce odpovědi nezáleží.

Důležité je:

* Text předaná rozhraní Translator Text API v textu požadavku
   * `Text`při použití metod Překladač, Transliterate a Slovníkvyhledávání
   * `Text`a `Translation` při použití metody Dictionary Examples
* Všechny značky: HTML, tagy XML atd. JSON zápis použitý k sestavení požadavku (například "Text:") se nepočítá.
* Individuální dopis
* Interpunkční znaménka
* Mezera, karta, značky a libovolný znak prázdného místa
* Každý bod kódu definovaný v Unicode
* Opakovaný překlad, i když jste přeložili stejný text dříve

U skriptů založených na ideogramech, jako je čínština a japonština Kanji, bude rozhraní API pro text překladače stále počítat počet bodů kódu Unicode, jeden znak na ideogram. Výjimka: Náhrady Unicode se počítají jako dva znaky.

Počet požadavků, slov, bajtů nebo vět je v počtu znaků irelevantní.

Volání Detect a BreakSentence metody nejsou započítány do spotřeby znaků. Očekáváme však, že volání Detect a BreakSentence metody jsou v přiměřeném poměru k použití jiných funkcí, které jsou počítány. Pokud počet volání Detect nebo BreakSentence překročí počet jiných počítaných metod 100krát, vyhrazuje si společnost Microsoft právo omezit vaše používání metod Detect a BreakSentence.


Další informace o počtu znaků naleznete v [nejčastějších dotazech k překladači společnosti Microsoft](https://www.microsoft.com/en-us/translator/faq.aspx).
