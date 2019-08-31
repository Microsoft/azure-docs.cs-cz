---
title: Omezení a hranice – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker má meta omezení pro části znalostní báze knowledge base a služby. Je důležité udržovat znalostní báze v rámci tyto limity, aby bylo možné testovat a publikovat.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/29/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 27526f4940cb7ab538992f3506c1a35a81cec9bc
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165023"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Omezení nástroje QnA Maker znalostní báze knowledge base a hranice

Níže uvedená omezení QnA Maker jsou kombinací [omezení cenové úrovně Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) a [omezení QnA maker cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Je potřeba znát obě sady omezení, abyste porozuměli tomu, kolik znalostní báze můžete vytvořit podle jednotlivých prostředků a jak velký může růst každý znalostní báze.

## <a name="knowledge-bases"></a>Znalostní báze

Maximální počet základů znalostní báze je založený na [limitech Azure Search vrstev](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Vrstva Azure Search** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD, HIGH DENSITY**|
|---|---|---|---|---|---|----|
|Maximální povolený počet publikovaných základů znalostí|2|14|49|199|199|2,999|

 Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 báze znalostí (1 index na publikovanou znalostní bázi). Patnáctý index `testkb`se používá pro všechny znalostní báze pro vytváření a testování. 

## <a name="extraction-limits"></a>Extrakce omezení

### <a name="maximum-number-of-files"></a>Maximální počet souborů

Maximální počet souborů, které se dají extrahovat a maximální velikost souboru, je založený na vašich **[QnA maker omezeních cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maximální počet přímých odkazů z adresy URL

Maximální počet přímých odkazů, které lze procházet pro extrakci QnAs ze stránky adresy URL, je **20**.

## <a name="metadata-limits"></a>Omezení metadat

### <a name="by-azure-search-pricing-tier"></a>Podle Azure Search cenové úrovně

Maximální počet polí metadat na bázi znalostní báze je založený na vašich **[omezeních Azure Search úrovně](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Vrstva Azure Search** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD, HIGH DENSITY**|
|---|---|---|---|---|---|----|
|Pole maximální metadat na službu QnA Maker (v rámci všech znalostní báze)|1 000|100 *|1 000|1 000|1 000|1 000|

### <a name="by-name-and-value"></a>Podle názvu a hodnoty

Délka a přijatelné znaky pro název a hodnotu metadat jsou uvedeny v následující tabulce.

|Položka|Povolené znaky|Porovnávání vzorů regulárního výrazu|Maximální počet znaků|
|--|--|--|--|
|Name|Poskytuje<br>alfanumerické znaky (písmena a číslice)<br>`_`podtržítkem|`^[a-zA-Z0-9_]+$`|100|
|Value|Umožňuje vše kromě<br>`:`kolon<br>`|`(vertikální svislá čára)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Omezení obsahu znalostní báze
Celkové limitů na obsah znalostní báze knowledge base:
* Délka textu odpovědi: 25,000
* Délka textu otázky: 1 000
* Délka textu klíč/hodnota metadat: 100
* Podporované znaky pro název metadat: Abecedy, číslice a`_`  
* Podporované znaky pro hodnotu metadat: Vše kromě `:` a`|` 
* Délka názvu souboru: 200
* Podporované formáty souborů: "TSV", ".pdf", ".txt", ".docx", ".xlsx".
* Maximální počet alternativních otázek: 300
* Maximální počet párů otázek a odpovědí: Závisí na zvolené **[úrovni Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Pár otázek a odpovědí se mapuje na dokument v indexu Azure Search. 
* Adresa URL/stránka HTML: 1 000 000 znaků

## <a name="create-knowledge-base-call-limits"></a>Vytvoření znalostní báze volání omezení:
Tyto představují vytvořit omezení pro každou akci znalostní báze; To znamená, že kliknete na *vytvořit KB* nebo volání rozhraní API CreateKnowledgeBase.
* Maximální počet alternativních otázek na jednu odpověď: 300
* Maximální počet adres URL: 10
* Maximální počet souborů: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizovat omezení volání znalostní báze
Představují limity pro každou akci aktualizace; To znamená, že kliknete na *uložit a jejich trénování* nebo volání rozhraní API UpdateKnowledgeBase.
* Délka každého názvu zdroje: 300
* Maximální počet přidaných nebo odstraněných alternativních otázek: 300
* Maximální počet přidaných nebo odstraněných polí metadat: 10
* Maximální počet adres URL, které lze aktualizovat: 5

## <a name="next-steps"></a>Další postup

Zjistěte, kdy a jak změnit úrovně služeb:

* [QnA maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Pokud potřebujete mít ve znalostní bázi více zdrojových souborů nebo větší dokumenty, než je vaše aktuální vrstva, upgradujte svou cenovou úroveň služby QnA Maker.
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Pokud vaše znalostní báze potřebuje k obsluze více požadavků z klientské aplikace, upgradujte si cenovou úroveň služby App Service.
* [Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Pokud plánujete, že máte spoustu znalostní báze, upgradujte si cenovou úroveň služby Azure Search.
