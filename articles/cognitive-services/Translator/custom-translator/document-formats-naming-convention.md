---
title: Formát dokumentu a konvence pojmenování – vlastní Translator
titleSuffix: Azure Cognitive Services
description: Toto je Průvodce na formát dokumentu a zásady vytváření názvů Translatoru Custom. Tento koncept pomáhá spravovat lepší abd předešel konfliktům názvů dokumenty.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 74b421691f5d3536c44af16e99dc54451dfde3f7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872760"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Zdokumentujte formátů a pojmenování pokyny konvence

Jakýkoli soubor používaný pro vlastní překládání musí být dlouhý aspoň **čtyři** znaků.

Tato tabulka obsahuje všechny podporované formáty souborů, které vám umožní vytvořit svůj systém překlad:

| Formát            | Rozšíření   | Popis                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF. XLIFF | Paralelní dokumentu formát exportu systémů překladu paměti. Použít jazyky jsou definovány v souboru.                                                                                                                                                              |
| TMX               | . TMX         | Paralelní dokumentu formát exportu systémů překladu paměti. Použít jazyky jsou definovány v souboru.                                                                                                                                                              |
| PSČ               | . PSČ         | PSČ je ve formátu souboru archivu.                                                                                                                                                                                                        |
| Locstudio         | . LCL         | Microsoft formát pro paralelní dokumenty                                                                                                                                                                                                                                      |
| Aplikace Microsoft Word    | . DOCX        | Dokument aplikace Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . SOUBOR PDF         | Adobe Acrobat PDF                                                                                                                                                                                                                                                |
| HTML              | . HTML. HTM  | Dokument HTML                                                                                                                                                                                                                                                                  |
| Textový soubor         | . TXT         | Textových souborů s kódováním UTF-16 nebo UTF-8                                                                                                                                                                                                                                             |
| Zarovnané textový soubor | . ZAROVNÁNÍ       | Rozšíření `.ALIGN` je zvláštní rozšíření, které můžete použít, pokud víte, že jsou věty v páru dokumentu perfektně zarovnaný. Pokud zadáte `.ALIGN` souboru, vlastní Translator nebude zarovnat vět za vás. |
| Excelový soubor        | . XLSX        | Soubor aplikace Excel (2013 nebo novější). První řádek / řádků tabulky by měl být kód jazyka.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Slovník formátů

Translator vlastní slovníky, podporuje všechny formáty souborů, ty jsou podporovány pro trénovací sada. Pokud používáte Excel slovníku, Zkontrolujte prosím, že první řádek / řádků tabulky by měl být kódech jazyků.

## <a name="zip-file-formats"></a>Formáty souboru ZIP

Dokumenty lze seskupené do souboru zip jeden a nahráli. Podporuje vlastní Translator zip formáty souborů (ZIP, GZ a TGZ).

Každý dokument v souboru zip s příponou TXT, HTML, HTM, PDF, DOCX, ZAROVNAT musí následovat tyto zásady vytváření názvů:

{název dokumentu} \_{kód jazyka} {název dokumentu} je název dokumentu, je {kód jazyka} LanguageID ISO (dvou znaků) označující, že dokument obsahuje věty v daném jazyce. Musí existovat podtržítko (_), než kód jazyka.

Například pokud chcete nahrát dva paralelní dokumenty v rámci zip pro anglické Španělština systému, soubory by měl být s názvem "data_en" a "data_es".

Soubory z paměti překladu (TMX XLF, XLIFF, LCL, XLSX) nemusí dodržovat zásady vytváření názvů konkrétní jazyk.  

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o [projektu](workspace-and-project.md#what-is-a-custom-translator-project) můžete vytvořit a spravovat je.
