---
title: Počty znaků – Translator Text API
titleSuffix: Azure Cognitive Services
description: Způsob, jakým Translator Text API počítá znaky.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e3a16d9272e75f9a94f5381c1681c036d177e0f6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595987"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak Translator Text API počítá znaky

Translator Text API počítá každý bod kódu Unicode vstupního textu jako znak. Každý překlad textu na jazyk se počítá jako samostatný překlad, i když se požadavek provedl v jediném volání rozhraní API, které se překládá do několika jazyků. Délka odpovědi nezáleží.

Jaké počty jsou:

* Text předaný do Translator Text API v těle žádosti
   * `Text`Při použití metod překladu, přepisu a slovníku vyhledávání
   * `Text`a `Translation` při použití metody Dictionary Examples
* Všechny značky: HTML, značky XML atd. v textovém poli textu žádosti. Zápis JSON použitý k vytvoření žádosti (například text:) se nepočítá.
* Jednotlivá písmena
* Interpunkce
* Mezera, tabulátor, značka a libovolný druh prázdného znaku
* Každý bod kódu definovaný v kódování Unicode
* Opakovaný překlad, i když jste dříve přeložili stejný text

Pro skripty založené na ideograms, jako je například čínština a japonština kanji, bude Translator Text API stále počítat počet kódových bodů Unicode, jeden znak na ideogram. Výjimka: Náhrada Unicode se počítá jako dva znaky.

Počet požadavků, slov, bajtů nebo vět není v počtu znaků podstatný.

Volání metod detekce a BreakSentence se ve spotřebě znaků nezapočítávají. Očekáváme ale, že volání metod detekce a BreakSentence jsou úměrná k používání dalších funkcí, které se počítají. Pokud počet vydaných volání detekce nebo BreakSentence překročí počet dalších výpočetních metod o 100 krát, společnost Microsoft si vyhrazuje právo omezit použití metod detekce a BreakSentence.


Další informace o počtu znaků najdete v tématu [Nejčastější dotazy k Microsoft translatoru](https://www.microsoft.com/en-us/translator/faq.aspx).
