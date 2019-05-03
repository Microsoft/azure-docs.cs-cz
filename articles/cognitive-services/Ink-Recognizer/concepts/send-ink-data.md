---
title: Data inkoustu posílat rozhraní API pro rozpoznávání rukopisu
titlesuffix: Azure Cognitive Services
description: Další informace o volání rozhraní API pro analyzátor Ink pro různé aplikace
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 5a212c7332d085c15baabef8650572162c47903d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027093"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Data inkoustu posílat rozhraní API pro rozpoznávání rukopisu 

Digitální rukopis odkazuje na technologie, které umožňují digitální reprezentace vstup například rukopisu a kreslení. To je obvykle opírá od digitizéru, který zachycuje pohybů vstupní zařízení, jako je stylus. Jak zařízení jsou dál umožněte bohaté digitální rukopisu, umělá inteligence a machine learning umožňuje rozpoznávání písemné formátů a text v libovolném kontextu. Rozhraní API pro rozpoznávání rukopisu umožňuje odeslat inkoustových tahů a získat podrobné informace o nich. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Vs rozhraní API pro rozpoznávání rukopisu. OCR služby

Rozhraní API pro rozpoznávání rukopisu nepoužívá optické Recognition(OCR) znak. OCR služby zpracování dat pixel z bitové kopie poskytnout rozpoznávání textu psaného rukou a text. To se někdy nazývá offline rozpoznávání. Místo toho rozhraní API pro rozpoznávání rukopisu vyžaduje digitálních inkoust stroke data, která jsou zachyceny při použití vstupního zařízení. Zpracování dat digitálních inkoust tímto způsobem může vytvářet ve srovnání s OCR služby přesnější výsledky rozpoznávání. 

## <a name="sending-ink-data"></a>Odesílání dat inkoustu

Rozhraní API pro rozpoznávání rukopisu vyžaduje souřadnice X a Y, které představují inkoustových tahů vytvořené vstupní zařízení, od okamžiku, dotkne povrch detekce při je zrušeno. Body každého tahu musí být řetězcový hodnot oddělených čárkou a být ve formátu JSON jako v příkladu níže. Kromě toho každého tahu musí mít jedinečné ID v každém požadavku. Pokud ID se opakuje v rámci stejné žádosti, rozhraní API vrátí chybu. Pro velmi přesné rozpoznávání výsledky máte alespoň osm číslic za desetinnou čárkou. Počátek (0; 0) plátna je považován za levém horním rohu plátna rukopisu.

> [!NOTE]
> V následujícím příkladu není platný kód JSON. Žádost o úplné JSON pro rozpoznávání rukopisu můžete najít na [Githubu](https://go.microsoft.com/fwlink/?linkid=2089909).
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Odpověď modulu pro rozpoznávání rukopisu

Rozhraní API pro rozpoznávání rukopisu vrátí odpověď analýzy o objekty, které uznává z obsahu rukopisu. Odpověď obsahuje rozpoznávání jednotek, které popisují vztahy mezi různé inkoustových tahů. Například tahy, které vytvářejí distinct, samostatné tvary bude obsažena v různých jednotkách. Každá jednotka obsahuje podrobné informace o jeho inkoustových tahů včetně rozpoznaný objektu, jeho souřadnice a další atributy kreslení.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Tvary rozpoznávaných rozhraní API pro rozpoznávání rukopisu

Rozhraní API pro rozpoznávání rukopisu můžete identifikovat nejčastěji používané tvary v poznámek. Obrázku níže ukazuje některé základní příklady. Úplný seznam tvary a další obsah inkoustu rozpoznávaných rozhraní API najdete v článku [článkem referenční dokumentace rozhraní API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![Seznam tvary rozpoznávaných rozhraní API pro rozpoznávání rukopisu](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Doporučené způsoby volání

Můžete volat rozhraní REST API pro rozpoznávání rukopisu v různé vzorce podle vaší aplikace. 

### <a name="user-initiated-api-calls"></a>Volání rozhraní API, kterou inicioval uživatel

Pokud vytváříte aplikaci, která využívá uživatelský vstup (například Poznámka odebírání nebo poznámky aplikace), možná budete chtít uživatelům umožnit při řízení a které rukopisu se odešlou do rozhraní API pro rozpoznávání rukopisu. Tato funkce je obzvláště užitečná při text i tvary jsou k dispozici na na plátno a uživatelé chtějí provádět různé akce u každého. Zvažte přidání funkce výběru (například nepravidelné oblasti nebo jiný nástroj pro výběr geometrické), které umožnit uživatelům si vybrat, co se odesílá do rozhraní API.  

### <a name="app-initiated-api-calls"></a>Aplikace iniciovala volání rozhraní API

Je také možné aplikaci volání rozhraní API pro rozpoznávání rukopisu po vypršení časového limitu. Když pravidelně odesílá aktuální inkoustových tahů rozhraní API, můžete ukládat výsledky rozpoznávání podle jejich vytvoření při současném zvyšování doba odezvy rozhraní API. Můžete například odeslat, že řádek rukou psaný text do rozhraní API potom, co detekovaly uživatel dokončil. 

S výsledky rozpoznávání předem poskytuje informace o vlastnostech inkoustových tahů jejich vztah k sobě navzájem. Například které tahy jsou seskupené do formuláře téhož slova, řádek, seznam, odstavce nebo tvar. Tyto informace můžete vylepšit nebudou moct vybrat skupiny tahů najednou, například funkce Výběr rukopisu vaší aplikace.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrace rozhraní API pro rozpoznávání rukopisu pomocí Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) poskytuje nástroje a technologie, které zvýší digitální prostředí rukopisu na mnoha různých zařízení. Platforma Windows Ink můžete kombinovat pomocí rozhraní API pro rozpoznávání rukopisu vytvářet aplikace, které zobrazí a interpretovat digitální inkoustových tahů.

## <a name="next-steps"></a>Další postup

* [Co je rozhraní API pro rozpoznávání rukopisu?](../overview.md)
* [Odkaz na rozhraní REST API pro rozpoznávání rukopisu](https://go.microsoft.com/fwlink/?linkid=2089907)

* Zahájit odesílání dat stroke digitálního inkoustu pomocí:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)