---
title: Odesílání dat do rozhraní API pro rozpoznávání rukopisu
titleSuffix: Azure Cognitive Services
description: Informace o volání rozhraní API analyzátoru inkoustu pro různé aplikace
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0ad961495d44f13522a3c02224a5612aaedaf076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221103"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Odesílání dat do rozhraní API pro rozpoznávání rukopisu 

Termín digitální rukopis označuje technologie, které umožňují digitální reprezentaci výstupu, jako jsou rukopis a kresby. Toho je obvykle dosaženo pomocí digitizéru, který zachycuje pohyby vstupních zařízení, jako je například stylus. Zařízení stále víc podporují bohatá prostředí digitálních rukopisů a umělá inteligence a strojové učení umožňují rozpoznávání psaných tvarů a textu v libovolném kontextu. Rozhraní API rozpoznávání rukopisu umožňuje odesílat tahy rukopisu a získat o nich podrobné informace. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Rozhraní API pro rozpoznávání rukopisu vs. ocr služby

Rozhraní API pro rozpoznávání rukopisu nepoužívá optické rozpoznávání znaků (OCR). Služby OCR zpracovávají data pixelů z obrázků, aby zajistily rozpoznávání rukopisu a textu. To se někdy nazývá offline rozpoznávání. Místo toho rozhraní API rozpoznávání rukopisu vyžaduje data digitálního tahu rukopisu, která jsou zachycena při použití vstupního zařízení. Zpracování digitálních inkoustových dat tímto způsobem může vést k přesnějším výsledkům rozpoznávání ve srovnání se službami OCR. 

## <a name="sending-ink-data"></a>Odesílání inkoustových dat

Rozhraní API rozpoznávání rukopisu vyžaduje souřadnice X a Y, které představují tahy rukopisu vytvořené vstupním zařízením, od okamžiku, kdy se dotkne detekční ho povrchu, až po jeho zvednutí. Body každého tahu musí být řetězec hodnot oddělených čárkami a musí být formátovány v JSON jako v níže uvedeném příkladu. Kromě toho musí mít každý tah perem v každé žádosti jedinečné ID. Pokud id se opakuje v rámci stejné žádosti, rozhraní API vrátí chybu. Pro nejpřesnější výsledky rozpoznávání, mají alespoň osm číslic za desetinnou čárkou. Počátek (0,0) plátna se považuje za levý horní roh plátna rukopisu.

> [!NOTE]
> Následující příklad není platný JSON. Úplný požadavek JSON pro rozpoznávání inkoustu najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089909).
 
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

## <a name="ink-recognizer-response"></a>Odpověď nástroje pro rozpoznávání rukopisu

Rozhraní API rozpoznávání rukopisu vrátí odpověď analýzy o objektech, které rozpoznala z obsahu rukopisu. Odpověď obsahuje jednotky rozpoznávání, které popisují vztahy mezi různými tahy perem. Například tahy, které vytvářejí odlišné, samostatné tvary, budou obsaženy v různých jednotkách. Každá jednotka obsahuje podrobné informace o svých tahcích perem, včetně rozpoznaného objektu, jeho souřadnic a dalších atributů výkresu.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Obrazce rozpoznané rozhraním API pro rozpoznávání rukopisu

Rozhraní API rozpoznávání rukopisu může identifikovat nejčastěji používané obrazce při pořizování poznámek. Níže uvedený obrázek ukazuje některé základní příklady. Úplný seznam obrazců a dalšího obsahu tiskového rozhraní rozpoznaného rozhraním API naleznete v [článku s odkazem na rozhraní API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![Seznam obrazců rozpoznaných rozhraním API pro rozpoznávání rukopisu](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Doporučené způsoby volání

Rozhraní REST API rozpoznávání rukopisu můžete volat v různých vzorcích podle vaší aplikace. 

### <a name="user-initiated-api-calls"></a>Volání rozhraní API iniciovaná uživatelem

Pokud vytváříte aplikaci, která přebírá vstup uživatele (například aplikaci pro pořizování poznámek nebo poznámky), můžete jim dát kontrolu nad tím, kdy a který rukopis se odešle do rozhraní API pro rozpoznávání rukopisu. Tato funkce je užitečná zejména v případě, že text i obrazce jsou na plátně přítomny a uživatelé chtějí pro každou z nich provádět různé akce. Zvažte přidání funkcí výběru (například laso nebo jiného nástroje pro geometrický výběr), které uživatelům umožní zvolit, co se odešle do rozhraní API.  

### <a name="app-initiated-api-calls"></a>Volání rozhraní API iniciovaná aplikací

Aplikaci můžete také po uplynutí časového času zavolat rozhraní API pro rozpoznávání rukopisu. Rutinním odesláním aktuálních tahů perem do rozhraní API můžete výsledky rozpoznávání při jejich vytváření ukládat a zároveň zlepšit dobu odezvy rozhraní API. Můžete například odeslat řádek ručně psaného textu do rozhraní API po zjištění, že jej uživatel dokončil. 

S výsledky rozpoznávání předem poskytuje informace o vlastnostech tahů inkoustu, které se vztahují k sobě navzájem. Například tahy, které jsou seskupeny tak, aby tvořily stejné slovo, řádek, seznam, odstavec nebo tvar. Tyto informace mohou vylepšit funkce výběru inkoustu aplikace například výběrem skupin tahů najednou.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrace rozhraní API pro rozpoznávání rukopisu s windows ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) poskytuje nástroje a technologie, které umožňují digitální rukopis na různých zařízeních. Platformu Windows Ink můžete kombinovat s rozhraním API pro rozpoznávání rukopisu a vytvářet tak aplikace, které zobrazují a interpretují digitální tahy rukopisu.

## <a name="next-steps"></a>Další kroky

* [Co je rozhraní API pro rozpoznávání rukopisu?](../overview.md)
* [Odkaz na rozhraní REST API nástroje pro rozpoznávání rukopisu](https://go.microsoft.com/fwlink/?linkid=2089907)

* Začněte odesílat data digitálního tahu perem pomocí:
    * [C #](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [Javascript](../quickstarts/javascript.md)
