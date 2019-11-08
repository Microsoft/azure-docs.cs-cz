---
title: Omezení a hranice – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker má meta omezení pro části znalostní báze a služby. Pro účely testování a publikování je důležité zachovat znalostní bázi v rámci těchto omezení.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 4effd14029eaaee1e1c22cdb814096820e19e089
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794032"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker limity a hranice znalostní báze

Níže uvedené limity QnA Maker jsou kombinací [omezení cenové úrovně Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) a [QnA maker limity cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Je potřeba znát obě sady omezení, abyste porozuměli tomu, kolik znalostní báze můžete vytvořit podle jednotlivých prostředků a jak velký může růst každý znalostní báze.

## <a name="knowledge-bases"></a>Znalostní báze

Maximální počet základů znalostní báze je založený na [omezeních vrstvy Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Úroveň Kognitivní hledání Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximální povolený počet publikovaných základů znalostí|2|14|49|199|199|2 999|

 Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 báze znalostí (1 index na publikovanou znalostní bázi). Patnáctý index `testkb`se používá pro všechny znalostní báze pro vytváření a testování. 

## <a name="extraction-limits"></a>Omezení extrakce

### <a name="maximum-number-of-files"></a>Maximální počet souborů

Maximální počet souborů, které se dají extrahovat a maximální velikost souboru, je založený na vašich **[QnA maker omezeních cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maximální počet přímých odkazů z adresy URL

Maximální počet přímých odkazů, které lze procházet pro extrakci QnAs ze stránky adresy URL, je **20**.

## <a name="metadata-limits"></a>Omezení metadat

### <a name="by-azure-cognitive-search-pricing-tier"></a>Podle cenové úrovně Azure Kognitivní hledání

Maximální počet polí metadat na bázi znalostní báze je založený na **[omezeních vaší vrstvy Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Úroveň Kognitivní hledání Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximální počet polí metadat na službu QnA Maker (napříč všemi aktualizací KB)|1 000|100 *|1 000|1 000|1 000|1 000|

### <a name="by-name-and-value"></a>Podle názvu a hodnoty

Délka a přijatelné znaky pro název a hodnotu metadat jsou uvedeny v následující tabulce.

|Položka|Povolené znaky|Porovnávání vzorů regulárního výrazu|Maximální počet znaků|
|--|--|--|--|
|Název|Poskytuje<br>alfanumerické znaky (písmena a číslice)<br>`_` (podtržítko)|`^[a-zA-Z0-9_]+$`|100|
|Hodnota|Umožňuje vše kromě<br>`:` (dvojtečka)<br>`|` (vertikální svislá čára)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Omezení obsahu znalostní báze
Celková omezení obsahu ve znalostní bázi Knowledge Base:
* Délka textu odpovědi: 25 000
* Délka textu otázky: 1 000
* Délka textu klíče a hodnoty metadat: 100
* Podporované znaky pro název metadat: abecedy, číslice a `_`  
* Podporované znaky pro hodnotu metadat: vše kromě `:` a `|` 
* Délka názvu souboru: 200
* Podporované formáty souborů: ". TSV", ". PDF", ". txt", ". docx", ". xlsx".
* Maximální počet alternativních otázek: 300
* Maximální počet párů otázek a odpovědí: závisí na zvolené **[úrovni Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Pár otázek a odpovědí se mapuje na dokument v Azure Kognitivní hledání indexu. 
* Adresa URL/stránka HTML: 1 000 000 znaků

## <a name="create-knowledge-base-call-limits"></a>Vytvořit limity volání ve znalostní bázi Knowledge Base:
Tyto hodnoty udávají omezení pro každou akci vytvoření znalostní báze. To znamená, že kliknete na *vytvořit KB* nebo zavolat rozhraní CreateKnowledgeBase API.
* Maximální počet alternativních otázek na odpověď: 300
* Maximální počet adres URL: 10
* Maximální počet souborů: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizovat omezení volání znalostní báze
Tyto hodnoty udávají omezení pro každou akci aktualizace. To znamená, že kliknete na *Uložit a naučíte* se volat rozhraní UpdateKnowledgeBase API.
* Délka každého názvu zdroje: 300
* Maximální počet přidaných nebo odstraněných alternativních otázek: 300
* Maximální počet přidaných nebo odstraněných polí metadat: 10
* Maximální počet adres URL, které lze aktualizovat: 5

## <a name="next-steps"></a>Další kroky

Naučte se, kdy a jak změnit [cenové úrovně služby](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).
