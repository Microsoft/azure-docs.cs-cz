---
title: Formáty dokumentů a konvence pojmenování – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Toto je průvodce formáty dokumentů a konvence pojmenování ve vlastním překladateli. Tento koncept pomáhá lépe spravovat názvy dokumentů a vyhnout se konfliktům názvů.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9598d3c1d339d1c37b02892cc83164acae447434
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895845"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Formáty dokumentů a pokyny k konvenci pojmenování

Každý soubor, který se používá pro vlastní překlad, musí mít délku alespoň **čtyři** znaky.

Tato tabulka obsahuje všechny podporované formáty souborů, které můžete použít k sestavení systému překladu:

| Formát            | Rozšíření   | Popis                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF, . XLIFF | Formát paralelního dokumentu, který exportuje systémy překladu paměti. Používané jazyky jsou definovány v souboru.                                                                                                                                                              |
| TMX               | . TMX         | Formát paralelního dokumentu, který exportuje systémy překladu paměti. Používané jazyky jsou definovány v souboru.                                                                                                                                                              |
| VĚŘITEL               | . VĚŘITEL         | ZIP je formát souboru archivu.                                                                                                                                                                                                        |
| Locstudio         | . LCL         | Formát Microsoft pro paralelní dokumenty                                                                                                                                                                                                                                      |
| Microsoft Word    | . DOCX        | Dokument aplikace Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . FORMÁTU         | Portable Document Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | . HTML,. SOUBORECH  | Dokument HTML                                                                                                                                                                                                                                                                  |
| Textový soubor         | . TXT         | Textové soubory kódované v kódování UTF-16 nebo UTF-8. Název souboru nesmí obsahovat japonské znaky.                                                                                                                                                                                        |
| Soubor zarovnaného textu | . VZTAHUJÍ       | Přípona `.ALIGN` je speciální rozšíření, které můžete použít, pokud víte, že věty v páru dokumentů jsou dokonale zarovnané. Pokud zadáte `.ALIGN` soubor, vlastní Překladatel nebude zarovnávat věty za vás. |
| Excelový soubor        | . XLSX        | Excelový soubor (2013 nebo novější). První řádek nebo řádek tabulky by měl být kód jazyka.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formáty slovníku

U slovníků podporuje vlastní Překladatel všechny formáty souborů, které jsou podporované pro školicí sady. Pokud používáte slovník aplikace Excel, první řádek nebo řádek tabulky by měl být jazykový kód.

## <a name="zip-file-formats"></a>Formáty souborů zip

Dokumenty mohou být seskupeny do jednoho souboru zip a nahrány. Vlastní Překladatel podporuje formáty souborů zip (ZIP, GZ a TGZ).

Každý dokument v souboru zip, který má příponu TXT, HTML, HTM, PDF, DOCX, musí být v souladu s těmito konvencemi vytváření názvů:

{název dokumentu} \_ {Code Language} kde {Document Name} je název vašeho dokumentu a {Language Code} je ISO LanguageID (dva znaky), což značí, že dokument obsahuje věty v tomto jazyce. Před kódem jazyka musí být znak podtržítka (_).

Například pro nahrání dvou paralelních dokumentů v rámci souboru zip pro angličtinu do španělštiny by měly být soubory pojmenované "data_en" a "data_es".

Soubory paměti pro překlad (TMX, XLF, XLIFF, LCL, XLSX) nejsou vyžadovány pro konkrétní jazykové konvence pojmenování.  

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [projektu](workspace-and-project.md#what-is-a-custom-translator-project) , abyste ho mohli vytvořit a spravovat.
