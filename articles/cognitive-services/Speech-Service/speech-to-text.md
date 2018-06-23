---
title: O převod řeči na Text | Microsoft Docs
description: Přehled možností převod řeči na Text rozhraní API.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 2eee1c6f9158f128ed5ffe575f8f498f1d3eb5e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343800"
---
# <a name="about-the-speech-to-text-api"></a>O převod řeči na Text rozhraní API

**Řeči na Text** rozhraní API *transcribes* příkaz zvukových datových proudů do text, který vaše aplikace můžete zobrazit uživateli nebo provedení akce jako vstup. Rozhraní API lze pomocí služby SDK klientské knihovny (pro podporované platformy a jazyky) nebo rozhraní REST API.

**Řeči na Text** rozhraní API nabízejí následující funkce:

- Pokročilé technologie rozpoznávání řeči od společnosti Microsoft – stejné používané Cortana, Office a další produkty společnosti Microsoft.

- Průběžné rozpoznávání v reálném čase. **Převod řeči na Text** umožňuje uživatelům transcribe zvuk do textu v reálném čase. Podporuje také příjem mezilehlých výsledků slova, která, pokud byly rozpoznány. Služba automaticky rozpozná konec řeči. Uživatelé mohou také další možnosti formátování, včetně použití velkých písmen a interpunkční znaménka, vulgárnost maskování a normalizaci text.

- Optimalizované **řeči na Text** výsledky pro interaktivní, konverzace a diktování scénáře. 

- Podpora pro mnoho mluvené jazyků v několika dialekty. Úplný seznam podporovaných jazyků v jednotlivých režimech rozpoznávání najdete v tématu [podporované jazyky](supported-languages.md#speech-to-text).

- Přizpůsobit jazyk a acoustic modely, takže můžete přizpůsobit aplikace na vašich uživatelů způsob mluvení, hovořícího prostředí a specializované terminologie.

- Principy přirozeném jazyce. Díky integraci s [znalosti jazyka](https://docs.microsoft.com/azure/cognitive-services/luis/) (LEOŠ), můžete odvodit tříd Intent a entity z řeči. Uživatelé nemusí vědět termínů vaší aplikace, ale popíše chtějí do svých vlastních slov.

## <a name="api-capabilities"></a>Funkce rozhraní API

Některé možnosti **řeči na Text** nejsou k dispozici prostřednictvím REST API. V následující tabulce najdete souhrn možností jednotlivých metod přístupu k rozhraní API.

| Případ použití | REST | Sady SDK |
|-----|-----|-----|----|
| Transcribe krátké utterance, třeba příkaz (délka < 15 s); žádné dočasné výsledky | Ano | Ano |
| Transcribe delší utterance (s > 15) | Ne | Ano |
| Transcribe datový proud zvuku s volitelné dočasné výsledky | Ne | Ano |
| Pochopení mluvčího záměry prostřednictvím LEOŠ | Ne\* | Ano |

\* *LEOŠ záměry a entity lze odvodit pomocí samostatné předplatné LEOŠ. S tímto odběrem sady SDK můžete volat LEOŠ a zadejte entity a záměr výsledků, jakož i řeči transcriptions. Pomocí rozhraní REST API, můžete volat LEOŠ sami odvození tříd Intent a entity s předplatným LEOŠ.*

## <a name="next-steps"></a>Další postup

* [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
* [Informace o tom, rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
