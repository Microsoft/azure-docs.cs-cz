---
title: Typy obsahu - QnA Maker
description: Typy obsahu zahrnují mnoho standardních strukturovaných dokumentů, jako je PDF, DOC a TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650193"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Typy obsahu dokumentů, které můžete přidat do znalostní báze
Typy obsahu zahrnují mnoho standardních strukturovaných dokumentů, jako je PDF, DOC a TXT.

## <a name="file-and-url-data-types"></a>Datové typy souborů a adres URL

Níže uvedená tabulka shrnuje typy obsahu a formátů souborů, které qnA maker podporuje.

|Typ zdroje|Typ obsahu| Příklady|
|--|--|--|
|zprostředkovatele identity|Nejčastější dotazy<br> (Byt, se sekcemi nebo s domovskou stránkou témat)<br>Stránky podpory <br> (Jednostránkové články s návody, články o odstraňování problémů atd.)|[Prosté nejčastější dotazy](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Nejčastější dotazy s odkazy](https://www.microsoft.com/en-us/software-download/faq),<br> [Nejčastější dotazy k domovskou stránku s tématy](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Článek podpory](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|Faq<br> Příručka k produktu,<br> Brožury<br> Papír<br> Zásady letáků,<br> Průvodce podporou,<br> Strukturované QnA,<br> atd.|**Bez víceotočení**<br>[Strukturovaná QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Vzorový návod k výrobě.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Ukázka polostrukturovaného dokumentu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Ukázka bílého papíru.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Vícenásobné otočení**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Výhody společnosti Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Výhody společnosti Contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Strukturovaný soubor QnA<br> (včetně rtf, podpora HTML)|[Ukázka nejčastějších dotazů QnA](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Strukturovaný soubor QnA|[Ukázka chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Pokud potřebujete ověření pro zdroj dat, zvažte následující metody, jak tento obsah dostat do QnA Makeru:

* Stažení souboru ručně a import do QnA Makeru
* Přidání souboru z ověřeného [umístění služby Sharepoint](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>Obsah adresy URL

Dva typy dokumentů lze importovat přes **ADRESU URL** v QnA Makeru:

* Adresy URL v častých dotazech
* Adresy URL podpory

Každý typ označuje očekávaný formát.

## <a name="file-based-content"></a>Obsah založený na souborech

Soubory můžete do znalostní báze přidat z veřejného zdroje nebo místního systému souborů na [portálu QnA Maker](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Pokyny pro formát obsahu

Přečtěte si další informace o [pokynech](../reference-document-format-guidelines.md) pro formát pro různé soubory.

## <a name="next-steps"></a>Další kroky

Zjistěte, jaké informace jsou uloženy v [sadě otázek a odpovědí (QnA).](question-answer-set.md)