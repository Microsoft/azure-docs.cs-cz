---
title: Formáty dokumentů a konvence pojmenování – vlastní Překladatel
titleSuffix: Azure Cognitive Services
description: Toto je průvodce formáty dokumentů a konvence pojmenování ve vlastním překladateli. Tento koncept pomáhá spravovat názvy dokumentů lépe Abd konflikty při pojmenovávání.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595850"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Formáty dokumentů a pokyny k konvenci pojmenování

Každý soubor, který se používá pro vlastní překlad, musí mít délku alespoň **čtyři** znaky.

Tato tabulka obsahuje všechny podporované formáty souborů, které můžete použít k sestavení systému překladu:

| Formát            | Rozšíření   | Popis                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF, . XLIFF | Formát paralelního dokumentu, který exportuje systémy překladu paměti. Používané jazyky jsou definovány v souboru.                                                                                                                                                              |
| TMX               | .TMX         | Formát paralelního dokumentu, který exportuje systémy překladu paměti. Používané jazyky jsou definovány v souboru.                                                                                                                                                              |
| VĚŘITEL               | . VĚŘITEL         | ZIP je formát souboru archivu.                                                                                                                                                                                                        |
| Locstudio         | .LCL         | Formát Microsoft pro paralelní dokumenty                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Dokument aplikace Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Portable Document Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | .HTML, .HTM  | Dokument HTML                                                                                                                                                                                                                                                                  |
| Textový soubor         | .TXT         | Textové soubory kódované v kódování UTF-16 nebo UTF-8. Název souboru nesmí obsahovat japonské znaky.                                                                                                                                                                                        |
| Soubor zarovnaného textu | . VZTAHUJÍ       | Přípona `.ALIGN` je speciální rozšíření, které můžete použít, pokud víte, že věty v páru dokumentů jsou dokonale zarovnané. Pokud zadáte `.ALIGN` soubor, vlastní Překladatel nebude zarovnávat věty za vás. |
| Excelový soubor        | .XLSX        | Excelový soubor (2013 nebo novější). První řádek nebo řádek tabulky by měl být kód jazyka.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formáty slovníku

U slovníků podporuje vlastní Překladatel všechny formáty souborů, které jsou podporované pro školicí sady. Pokud používáte slovník aplikace Excel, první řádek nebo řádek tabulky by měl být jazykový kód.

## <a name="zip-file-formats"></a>Formáty souborů zip

Dokumenty mohou být seskupeny do jednoho souboru zip a nahrány. Vlastní Překladatel podporuje formáty souborů zip (ZIP, GZ a TGZ).

Každý dokument v souboru zip, který má příponu TXT, HTML, HTM, PDF, DOCX, musí být v souladu s těmito konvencemi vytváření názvů:

{název dokumentu} \_{Code Language} kde {Document Name} je název vašeho dokumentu a {Language Code} je ISO LanguageID (dva znaky), což značí, že dokument obsahuje věty v tomto jazyce. Před kódem jazyka musí být znak podtržítka (_).

Například pro nahrání dvou paralelních dokumentů v rámci souboru zip pro angličtinu do španělštiny by měly být soubory pojmenovány "data_en" a "data_es".

Soubory paměti pro překlad (TMX, XLF, XLIFF, LCL, XLSX) nejsou vyžadovány pro konkrétní jazykové konvence pojmenování.  

## <a name="next-steps"></a>Další postup

- Přečtěte si o [projektu](workspace-and-project.md#what-is-a-custom-translator-project) , abyste ho mohli vytvořit a spravovat.
