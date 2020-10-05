---
title: Co je nástroj pro rozpoznávání rukopisu? – Rozhraní API pro rozpoznávání rukopisu
titleSuffix: Azure Cognitive Services
description: Integrujte Nástroj pro rozpoznávání rukopisu do aplikací, webů, nástrojů a dalších řešení, aby bylo možné identifikovat a používat data tahů perem jako vstup.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6c1a720e7e9bd9c71f925f104ca7fc70a1a5ef59
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89051062"
---
# <a name="what-is-the-ink-recognizer-api"></a>Co je rozhraní API pro rozpoznávání rukopisu?

[!INCLUDE [ink-recognizer-deprecation](includes/deprecation-note.md)]

Služba rozpoznávání rukopisu poskytuje cloudové REST API k analýze a rozpoznávání obsahu digitálního inkoustu. Na rozdíl od služeb, které používají optické rozpoznávání znaků (OCR), vyžaduje rozhraní API jako vstup data digitálního tahu perem. Digitální tahy perem jsou časově uspořádané sady 2D bodů (souřadnice X, Y), které představují pohyb nástrojů vstupu, jako jsou například digitální pera nebo prsty. Pak rozpoznává obrazce a ručně psaný obsah ze vstupu a vrátí odpověď JSON obsahující všechny rozpoznané entity.

![Vývojový diagram popisující odeslání vstupu tahu perem do rozhraní API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Funkce

Pomocí rozhraní API pro rozpoznávání rukopisu můžete snadno rozpoznat rukou psaný obsah v aplikacích. 

|Funkce  |Popis  |
|---------|---------|
| Rozpoznávání rukopisu | Rozpoznávání rukopisného obsahu v 63 základních [jazycích a národních prostředích](language-support.md) | 
| Rozpoznávání rozložení | Získat strukturální informace o obsahu digitálního inkoustu Rozdělte obsah do oblasti psaní oblastí, odstavců, řádků, slov a seznamů s odrážkami. Vaše aplikace pak mohou pomocí informací o rozložení sestavovat další funkce, jako je automatické formátování seznamu a zarovnání tvarů. |
| Rozpoznávání obrazců | Při pořizování poznámek rozpoznáváme nejčastěji používané [geometrické obrazce](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) . |
| Kombinované tvary a rozpoznávání textu | Rozpoznat, které tahy perem patří k obrazcům nebo ručnímu obsahu, a samostatně je klasifikovat.|

## <a name="workflow"></a>Pracovní postup

Rozhraní API pro rozpoznávání rukopisu je webová služba RESTful, která usnadňuje volání ze všech programovacích jazyků, které mohou provádět požadavky HTTP a analyzovat JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Po registraci:

1. Vybere data tahu perem a [naformátuje je](concepts/send-ink-data.md#sending-ink-data) na platný formát JSON. Rozhraní API akceptuje až 1500 tahů perem na požadavek. 
1. Odešlete požadavek na rozhraní API pro rozpoznávání rukopisu pomocí vašich dat.
1. Zpracujte odpověď rozhraní API parsováním vrácené zprávy JSON.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si rychlý Start v následujících jazycích, abyste mohli začít volat rozhraní API pro rozpoznávání rukopisu.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

Pokud chcete zjistit, jak funguje rozhraní API pro rozpoznávání rukopisu v digitální aplikaci pro rukopis, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
