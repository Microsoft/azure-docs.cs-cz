---
title: Co je překlad dokumentů?
description: Přehled cloudové služby a procesu překladu dokumentů Batch.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: fc75bfc3b79cd6aed51a44fc47787532a9d61e8b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713010"
---
# <a name="what-is-document-translation-preview"></a>Co je překlad dokumentu (Preview)?

Překlad dokumentů je cloudová funkce služby [Azure Translator](../translator-info-overview.md) , která je součástí řady rozhraní REST API služby Azure pro rozpoznávání. Rozhraní API pro překlad dokumentů Převádí dokumenty do a z 90 jazyků a dialektů a současně zachovává strukturu dokumentů a formát dat.

## <a name="document-translation-key-features"></a>Funkce klíče pro překlad dokumentu

| Funkce | Popis |
| ---------| -------------|
| **Překlad velkých souborů**| Převede všechny dokumenty asynchronně.|
|**Přeložit množství souborů**|Přeloží více souborů na a z 90 jazyků a dialektů.|
|**Zachovat prezentaci zdrojového souboru**| Přeloží soubory a zachová původní rozložení a formát.|
|**Použít vlastní překlad**| Přeloží dokumenty pomocí obecných a [vlastních modelů překladu](../customization.md#custom-translator) .|
|**Použít vlastní Glossaries**|Přeloží dokumenty pomocí vlastních Glossaries.|

## <a name="how-to-get-started"></a>Jak začít?

V průvodci návodem se dozvíte, jak rychle začít používat překladatele dokumentů. Začněte tím, že budete potřebovat aktivní [účet Azure](https://azure.microsoft.com/free/cognitive-services/).  Pokud ho nemáte, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free).

> [!div class="nextstepaction"]
> [Začínáme](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>Podporované formáty dokumentů

Překlad dokumentu podporuje následující typy souborů dokumentů:

| Typ souboru| Přípona souboru|Popis|
|---|---|--|
|Adobe PDF|.pdf|Formát formátu PDF aplikace Adobe Acrobat|
|HTML|.html,|Technologie Hyper Text Markup Language.|
|Formát souborů pro výměnu lokalizace|.xlf. , XLIFF| Formát paralelního dokumentu, který exportuje systémy překladu paměti. Používané jazyky jsou definovány v souboru.|
|Microsoft Excel|.xlsx|Soubor tabulky pro analýzu a dokumentaci k datům.|
|Microsoft Outlook|. msg|E-mailová zpráva vytvořená nebo uložená v aplikaci Microsoft Outlook.|
|Microsoft PowerPoint|.pptx| Prezentační soubor, který slouží k zobrazení obsahu ve formátu prezentace.|
|Microsoft Word|.docx| Textový soubor dokumentu.|
|Tabulátory nebo hodnoty oddělené tabulátorem|. TSV/. Tab| soubor nezpracovaných dat oddělený tabulátorem používaný tabulkovými aplikacemi.|
|Text|.txt| Neformátovaný textový dokument.|
|Výměna paměti pro překlad|.tmx|Open XML standard, který se používá pro výměnu dat překladu paměti (TM) vytvořených počítačovým překladem (CAT) a lokalizaci aplikací.|

## <a name="supported-glossary-formats"></a>Podporované formáty glosáře

Překlad dokumentu podporuje následující typy souborů glosáře:

| Typ souboru| Přípona souboru|Popis|
|---|---|--|
|Formát souborů pro výměnu lokalizace|.xlf. , XLIFF| Formát paralelního dokumentu, který exportuje systémy překladu paměti. Používané jazyky jsou definovány v souboru.|
|Tabulátory nebo hodnoty oddělené tabulátorem|. TSV/. Tab| soubor nezpracovaných dat oddělený tabulátorem používaný tabulkovými aplikacemi.|

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s překladem dokumentů](get-started-with-document-translation.md)
>
>
