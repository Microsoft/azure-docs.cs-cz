---
title: Co je rozpoznávač inkoustu? - Rozhraní API pro rozpoznávání inkoustu
titleSuffix: Azure Cognitive Services
description: Integrujte rozpoznávání rukopisu do svých aplikací, webů, nástrojů a dalších řešení, abyste mohli identifikovat a použít data tahu perem a použít je jako vstup.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2d00a489af35f5960aaeb1dda7add5337d48d57d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448152"
---
# <a name="what-is-the-ink-recognizer-api"></a>Co je rozhraní API pro rozpoznávání rukopisu?


Služba Rozpoznávání rukopisu Cognitive Service poskytuje cloudové rozhraní REST API pro analýzu a rozpoznávání obsahu digitálního inkoustu. Na rozdíl od služeb, které používají optické rozpoznávání znaků (OCR), rozhraní API vyžaduje data digitálního tahu perem jako vstup. Tahy digitálních per jsou časově uspořádané sady 2D bodů (souřadnice X,Y), které představují pohyb vstupních nástrojů, jako jsou digitální pera nebo prsty. Potom rozpozná tvary a ručně psaný obsah ze vstupu a vrátí odpověď JSON obsahující všechny rozpoznané entity.

![Vývojový diagram popisující odeslání vstupu tahu perem do rozhraní API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Funkce

Pomocí rozhraní API pro rozpoznávání rukopisu můžete snadno rozpoznat ručně psaný obsah ve vašich aplikacích. 

|Funkce  |Popis  |
|---------|---------|
| Rozpoznávání rukopisu | Rozpoznávejte ručně psaný obsah v 63 základních [jazycích a národních prostředích](language-support.md). | 
| Rozpoznávání rozvržení | Získejte strukturální informace o obsahu digitálního inkoustu. Rozdělte obsah na oblasti zápisu, odstavce, řádky, slova, seznamy s odrážkami. Aplikace pak mohou pomocí informací o rozložení vytvářet další funkce, jako je automatické formátování seznamu a zarovnání tvarů. |
| Rozpoznávání obrazců | Rozpoznávejte nejčastěji používané [geometrické tvary](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) při pořizování poznámek. |
| Kombinované tvary a rozpoznávání textu | Rozpoznejte tahy rukopisu, které patří k tvarům nebo ručně psanému obsahu, a samostatně je klasifikujte.|

## <a name="workflow"></a>Pracovní postup

Rozhraní API rozpoznávání rukopisu je webová služba RESTful, což usnadňuje volání z libovolného programovacího jazyka, který může provádět požadavky HTTP a analyzovat JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Po registraci:

1. Vezměte data tahu inkoustu a [naformátujte je](concepts/send-ink-data.md#sending-ink-data) do platného JSON.
1. Odešlete žádost do rozhraní API pro rozpoznávání rukopisu s vašimi daty.
1. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

Zkuste rychlý start v následujících jazycích začít volat do rozhraní API rozpoznávání rukopisu.
* [C #](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [Javascript](quickstarts/javascript.md)

Pokud se chcete podívat, jak rozhraní API pro rozpoznávání rukopisu funguje v digitální aplikaci rukopisu, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
