---
title: Co je nástroj pro rozpoznávání rukopisu? -Rozhraní API pro rozpoznávání inkoustu
titlesuffix: Azure Cognitive Services
description: Nástroj pro rozpoznávání rukopisu integrate do vašich aplikací, webů, nástrojů a dalších řešení poskytují...
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ed1a72a5dc61458200b72c768ad722656b820d8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027226"
---
# <a name="what-is-the-ink-recognizer-api"></a>Co je rozhraní API pro rozpoznávání rukopisu?


Inkoust Rozlišovač služby Cognitive Services poskytuje cloudové rozhraní API REST k analýze a rozpoznávání digitálních inkoust obsahu. Na rozdíl od služeb, které používají optického rozpoznávání znaků (OCR) rozhraní API vyžaduje digitálních inkoust stroke data jako vstup. Digitální inkoustových tahů jsou časově řazenou sady 2D bodů (souřadnice X, Y), které představují pohybu vstupní nástrojů, jako je digitální pera nebo prstů. Potom rozpozná tvary a rukou psaný obsah ze vstupu a vrátí odpověď ve formátu JSON obsahující všechny rozpoznaný entitu.

![Vývojový diagram popisující odesílání inkoustu stroke vstupní hodnota pro rozhraní API](media/ink-recognizer-pen-graph.png)

## <a name="features"></a>Funkce

S rozhraním API pro rozpoznávání rukopisu můžete snadno rozpoznat ručně psaného obsahu ve vašich aplikacích. 

|Funkce  |Popis  |
|---------|---------|
| Rozpoznávání ručně psaného textu | Rozpoznávání rukopisného obsahu v 63 core [jazyky a národní prostředí](language-support.md). | 
| Rozpoznávání rozložení | Získejte strukturální informace o digitálních inkoust obsah. Rozdělte obsah na zápis oblastí, odstavce, řádky, slova, seznamy s odrážkami. Aplikace můžete potom rozložení informace použít k vytvoření dalších funkcí jako automatické seznam formátování a obrazce zarovnání. |
| Rozpoznávání obrazce | Rozpoznat nejčastěji používané [geometrické tvary](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) při pořizování poznámek. |
| Kombinované tvary a rozpoznávání textu | Rozpoznávání rukopisu, které tahy patří do tvarů nebo ručně psaného obsahu a jejich klasifikování samostatně.|

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro rozpoznávání rukopisu je RESTful webová služba, což usnadňuje volat z libovolného programovacího jazyka, který může vytvářet požadavky HTTP a parsování formátu JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Po registraci:

1. Posuňte svá data stroke inkoustu a [naformátování](concepts/send-ink-data.md#sending-ink-data) do platný kód JSON.
1. Odeslat požadavek na rozhraní API pro rozpoznávání rukopisu s vašimi daty.
1. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další postup

Vyzkoušejte si rychlé zprovoznění v těchto jazycích zahájíte volání rozhraní API pro rozpoznávání rukopisu.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/csharp.md)

Pokud chcete zobrazit, jak rozhraní API pro rozpoznávání rukopisu funguje v digitální rukopisu aplikaci, podívejte se na následující ukázkové aplikace na Githubu:
* [C#a Platform(UWP) Universal Windows](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C#a Windows Presentation Foundation(WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webový prohlížeč aplikace jazyka JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Kód SWIFT a mobilní aplikaci pro iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
