---
title: Formáty dokumentů a konvence pojmenování – vlastní překladač
titleSuffix: Azure Cognitive Services
description: Toto je průvodce formáty dokumentů a konvence pojmenování v vlastní překladač. Tento koncept pomáhá spravovat názvy dokumentů lépe abd vyhnout konflikty názvů.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595850"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Formáty dokumentů a pokyny k vytváření zásad pojmenování

Každý soubor použitý pro vlastní překlad musí mít délku alespoň **čtyři** znaky.

Tato tabulka obsahuje všechny podporované formáty souborů, které můžete použít k vytvoření překladového systému:

| Formát            | Rozšíření   | Popis                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . Xlf. XLIFF | Paralelní formát dokumentu, export systémů překladové paměti. Použité jazyky jsou definovány uvnitř souboru.                                                                                                                                                              |
| Tmx               | . Tmx         | Paralelní formát dokumentu, export systémů překladové paměti. Použité jazyky jsou definovány uvnitř souboru.                                                                                                                                                              |
| Zip               | . Zip         | ZIP je formát archivního souboru.                                                                                                                                                                                                        |
| Locstudio         | . Lcl         | Formát společnosti Microsoft pro paralelní dokumenty                                                                                                                                                                                                                                      |
| Microsoft Word    | . Docx        | Dokument aplikace Microsoft Word                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . Pdf         | Přenosný dokument aplikace Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | . Html. HTM  | Dokument HTML                                                                                                                                                                                                                                                                  |
| Textový soubor         | . Txt         | UTF-16 nebo UTF-8 kódované textové soubory. Název souboru nesmí obsahovat japonské znaky.                                                                                                                                                                                        |
| Zarovnaný textový soubor | . Zarovnat       | Rozšíření `.ALIGN` je speciální rozšíření, které můžete použít, pokud víte, že věty v páru dokumentu jsou dokonale zarovnány. Pokud zadáte `.ALIGN` soubor, vlastní překladač nebude zarovnat věty za vás. |
| Excelový soubor        | . Xlsx        | (2013 nebo novější). První řádek/ řádek tabulky by měl být kód jazyka.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Formáty slovníku

Pro slovníky vlastní překladač podporuje všechny formáty souborů, které jsou podporovány pro trénovací sady. Pokud používáte slovník aplikace Excel, první řádek / řádek tabulky by měly být kódy jazyků.

## <a name="zip-file-formats"></a>Formáty souborů ZIP

Dokumenty lze seskupit do jednoho souboru zip a nahrát. Vlastní překladač podporuje formáty souborů ZIP (ZIP, GZ a TGZ).

Každý dokument v souboru zip s příponou TXT, HTML, HTM, PDF, DOCX, ALIGN musí dodržovat tuto konvenci pojmenování:

{název dokumentu} \_{kód jazyka}, kde {název dokumentu} je název dokumentu, {kód jazyka} je ISO LanguageID (dva znaky), což znamená, že dokument obsahuje věty v tomto jazyce. Před kódem jazyka musí být podtržítko (_).

Chcete-li například nahrát dva paralelní dokumenty v rámci zip pro systém angličtina do španělštiny, soubory by měly být pojmenovány "data_en" a "data_es".

Soubory překladové paměti (TMX, XLF, XLIFF, LCL, XLSX) nemusí dodržovat konkrétní konvence pojmenování jazyka.  

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [projektu,](workspace-and-project.md#what-is-a-custom-translator-project) který je chcete vytvořit a spravovat.
