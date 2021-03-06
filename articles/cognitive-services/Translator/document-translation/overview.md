---
title: Co je překlad dokumentů?
description: Přehled cloudové služby a procesu překladu dokumentů Batch.
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 0d9ef13de29ac140d94e9e4c05b14f35b9e5834c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968165"
---
# <a name="what-is-document-translation-preview"></a>Co je překlad dokumentu (Preview)?

Překlad dokumentů je cloudová funkce služby [Azure Translator](../translator-info-overview.md) , která je součástí řady rozhraní REST API služby Azure pro rozpoznávání. Rozhraní API pro překlad dokumentů Převádí dokumenty do a z 90 jazyků a dialektů a současně zachovává strukturu dokumentů a formát dat.

Tato dokumentace obsahuje následující typy článků:  

* [**Rychlé starty**](get-started-with-document-translation.md) jsou pokyny Začínáme, které vám pomohou při provádění požadavků na službu.
* Návody obsahují pokyny k použití této funkce v konkrétnějším nebo přizpůsobeném [**způsobu.**](create-sas-tokens.md)  

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

| Typ souboru| Přípona souboru|Description|
|---|---|--|
|Adobe PDF|.pdf|Formát formátu PDF aplikace Adobe Acrobat|
|HTML|.html,|Technologie Hyper Text Markup Language.|
|Formát souborů pro výměnu lokalizace|.xlf. , XLIFF| Formát paralelního dokumentu, který exportuje systémy překladu paměti. Používané jazyky jsou definovány v souboru.|
|Microsoft Excel|.xlsx|Soubor tabulky pro analýzu a dokumentaci k datům.|
|Microsoft Outlook|. msg|E-mailová zpráva vytvořená nebo uložená v aplikaci Microsoft Outlook.|
|Microsoft PowerPoint|.pptx| Prezentační soubor, který slouží k zobrazení obsahu ve formátu prezentace.|
|Microsoft Word|.docx| Textový soubor dokumentu.|
|Tabulátory nebo hodnoty oddělené tabulátorem|. TSV/. Tab| Soubor nezpracovaných dat oddělený tabulátorem používaný tabulkovými aplikacemi.|
|Text|.txt| Neformátovaný textový dokument.|

## <a name="supported-glossary-formats"></a>Podporované formáty glosáře

Překlad dokumentu podporuje následující typy souborů glosáře:

| Typ souboru| Přípona souboru|Description|
|---|---|--|
|Formát souborů pro výměnu lokalizace|.xlf. , XLIFF| Formát paralelního dokumentu, který exportuje systémy překladu paměti. Používané jazyky jsou definovány v souboru.|
|Tabulátory nebo hodnoty oddělené tabulátorem|. TSV/. Tab| Soubor nezpracovaných dat oddělený tabulátorem používaný tabulkovými aplikacemi.|

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s překladem dokumentů](get-started-with-document-translation.md)
