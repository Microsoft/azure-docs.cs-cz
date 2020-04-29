---
title: Počty znaků – Translator Text API
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak Translator Text API Cognitive Services Azure počítá znaky, abyste mohli pochopit, jak ingestuje obsah.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "73888158"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak Translator Text API počítá znaky

Translator Text API počítá každý bod kódu Unicode vstupního textu jako znak. Každý překlad textu na jazyk se počítá jako samostatný překlad, i když se požadavek provedl v jediném volání rozhraní API, které se překládá do několika jazyků. Délka odpovědi nezáleží.

Jaké počty jsou:

* Text předaný do Translator Text API v těle žádosti
   * `Text`Při použití metod překladu, přepisu a slovníku vyhledávání
   * `Text`a `Translation` při použití metody Dictionary Examples
* Všechny značky: HTML, XML tagy atd. v textovém poli textu žádosti. Zápis JSON použitý k vytvoření žádosti (například text:) se nepočítá.
* Jednotlivá písmena
* Interpunkční znaménka
* Mezera, tabulátor, značka a libovolný druh prázdného znaku
* Každý bod kódu definovaný v kódování Unicode
* Opakovaný překlad, i když jste dříve přeložili stejný text

Pro skripty založené na ideograms, jako je například čínština a japonština kanji, bude Translator Text API stále počítat počet kódových bodů Unicode, jeden znak na ideogram. Výjimka: náhrada Unicode se počítá jako dva znaky.

Počet požadavků, slov, bajtů nebo vět není v počtu znaků podstatný.

Volání metod detekce a BreakSentence se ve spotřebě znaků nezapočítávají. Očekáváme ale, že volání metod detekce a BreakSentence jsou úměrná k používání dalších funkcí, které se počítají. Pokud počet vydaných volání detekce nebo BreakSentence překročí počet dalších výpočetních metod o 100 krát, společnost Microsoft si vyhrazuje právo omezit použití metod detekce a BreakSentence.


Další informace o počtu znaků najdete v tématu [Nejčastější dotazy k Microsoft translatoru](https://www.microsoft.com/en-us/translator/faq.aspx).
