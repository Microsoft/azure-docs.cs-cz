---
title: Typy obsahu – QnA Maker
description: Mezi typy obsahu patří mnoho standardních strukturovaných dokumentů, jako jsou soubory PDF, DOCX a TXT.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 50924ce035d9a7dc7778cf45668dc993ee5486e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776982"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Typy obsahu dokumentů, které můžete přidat do znalostní báze
Mezi typy obsahu patří mnoho standardních strukturovaných dokumentů, jako jsou soubory PDF, DOC a TXT.

## <a name="file-and-url-data-types"></a>Datové typy souborů a adres URL

Následující tabulka shrnuje typy obsahu a formátů souborů, které jsou podporovány nástrojem QnA Maker.

|Typ zdroje|Typ obsahu| Příklady|
|--|--|--|
|URL|Nejčastější dotazy<br> (Plochý, s oddíly nebo s domovskou stránkou témat)<br>Stránky podpory <br> (Články s návody na jednu stranu, články týkající se řešení potíží atd.)|[Jednoduché Nejčastější dotazy](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Nejčastější dotazy s odkazy](https://www.microsoft.com/en-us/software-download/faq)<br> [Nejčastější dotazy s domovskou stránkou s tématy](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Článek podpory](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|Nejčastější<br> Ruční produkt,<br> Brožury,<br> Formátech<br> Zásady oznámení<br> Průvodce podporou,<br> Strukturované QnA,<br> Cokoli dalšího|**Bez vícenásobného zapnutí**<br>[Strukturované QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Ukázkový produkt Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Ukázka semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Ukázka bílého paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Vícenásobné zapínání**:<br>[Plocha pro (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Výhody společnosti Contoso (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Výhody společnosti Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Strukturovaný soubor QnA<br> (včetně formátu RTF, HTML support)|**Bez vícenásobného zapínání**:<br>[Ukázka FAQ.xlsQnA ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Vícenásobné zapínání**:<br>[Strukturované jednoduché FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[FAQ.xlsSurface přenosného počítače ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Strukturovaný soubor QnA|[Ukázka Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Pokud pro zdroj dat potřebujete ověření, zvažte následující metody pro získání tohoto obsahu do QnA Maker:

* Stáhnout soubor ručně a importovat do QnA Maker
* Přidat soubor z ověřeného [umístění služby SharePoint](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>Obsah adresy URL

Pomocí **adresy URL** v QnA maker lze importovat dva typy dokumentů:

* Adresy URL pro nejčastější dotazy
* Adresy URL podpory

Každý typ indikuje očekávaný formát.

## <a name="file-based-content"></a>Obsah založený na souborech

Do znalostní báze můžete přidat soubory z veřejného zdroje nebo místního systému souborů na [portálu QnA maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Pokyny k formátu obsahu

Přečtěte si další informace o [pokynech pro formát](../reference-document-format-guidelines.md) pro různé soubory.

## <a name="next-steps"></a>Další kroky

Zjistěte, jaké informace jsou uloženy v [páru otázek a odpovědí (QnA)](question-answer-set.md).
