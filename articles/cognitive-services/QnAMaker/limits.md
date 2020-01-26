---
title: Omezení a hranice – QnA Maker
description: Nástroj QnA Maker má meta omezení pro části znalostní báze knowledge base a služby. Je důležité udržovat znalostní báze v rámci tyto limity, aby bylo možné testovat a publikovat.
ms.topic: article
ms.date: 01/23/2020
ms.openlocfilehash: 32e12d9e983795d2fb403a5b6bc304289ece92c2
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760228"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Omezení nástroje QnA Maker znalostní báze knowledge base a hranice

Níže uvedené limity QnA Maker jsou kombinací [omezení cenové úrovně Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) a [QnA maker limity cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Je potřeba znát obě sady omezení, abyste porozuměli tomu, kolik znalostní báze můžete vytvořit podle jednotlivých prostředků a jak velký může růst každý znalostní báze.

## <a name="knowledge-bases"></a>Znalostní báze

Maximální počet základů znalostní báze je založený na [omezeních vrstvy Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Úroveň Kognitivní hledání Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD, HIGH DENSITY**|
|---|---|---|---|---|---|----|
|Maximální povolený počet publikovaných základů znalostí|2|14|49|199|199|2,999|

 Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 báze znalostí (1 index na publikovanou znalostní bázi). Patnáctý index `testkb`se používá pro všechny znalostní báze pro vytváření a testování.

## <a name="extraction-limits"></a>Extrakce omezení

### <a name="file-naming-constraints"></a>Omezení pojmenovávání souborů

Názvy souborů nesmí obsahovat tyto znaky:

|Nepoužívat znak|
|--|
|`'` jednoduché uvozovky|
|`"` dvojité uvozovky|

### <a name="maximum-file-size"></a>Maximální velikost souboru

|Formát|Maximální velikost souboru (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximální počet souborů

Maximální počet souborů, které se dají extrahovat a maximální velikost souboru, je založený na vašich **[QnA maker omezeních cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maximální počet přímých odkazů z adresy URL

Maximální počet přímých odkazů, které lze procházet pro extrakci QnAs ze stránky adresy URL, je **20**.

## <a name="metadata-limits"></a>Omezení metadat

### <a name="by-azure-cognitive-search-pricing-tier"></a>Podle cenové úrovně Azure Kognitivní hledání

Maximální počet polí metadat na bázi znalostní báze je založený na **[omezeních vaší vrstvy Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Úroveň Kognitivní hledání Azure** | **Free** | **Basic** |**S1** | **S2**| **S3** |**S3 HD, HIGH DENSITY**|
|---|---|---|---|---|---|----|
|Pole maximální metadat na službu QnA Maker (v rámci všech znalostní báze)|1 000|100 *|1 000|1 000|1 000|1 000|

### <a name="by-name-and-value"></a>Podle názvu a hodnoty

Délka a přijatelné znaky pro název a hodnotu metadat jsou uvedeny v následující tabulce.

|Položka|Povolené znaky|Porovnávání vzorů regulárního výrazu|Maximální počet znaků|
|--|--|--|--|
|Name (Název)|Poskytuje<br>alfanumerické znaky (písmena a číslice)<br>`_` (podtržítko)|`^[a-zA-Z0-9_]+$`|100|
|Hodnota|Umožňuje vše kromě<br>`:` (dvojtečka)<br>`|` (vertikální svislá čára)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Omezení obsahu znalostní báze
Celkové limitů na obsah znalostní báze knowledge base:
* Délka textu odpovědi: 25 000
* Délka textu otázku: 1 000
* Délka textu klíč/hodnota metadat: 100
* Podporované znaky pro název metadat: abecedy, číslice a `_`
* Podporované znaky pro hodnotu metadat: vše kromě `:` a `|`
* Délka názvu souboru: 200
* Podporované formáty souborů: "TSV", ".pdf", ".txt", ".docx", ".xlsx".
* Maximální počet alternativních otázek: 300
* Maximální počet párů otázek a odpovědí: závisí na zvolené **[úrovni Azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Pár otázek a odpovědí se mapuje na dokument v Azure Kognitivní hledání indexu.
* Adresa URL/stránka HTML: 1 000 000 znaků

## <a name="create-knowledge-base-call-limits"></a>Vytvoření znalostní báze volání omezení:
Tyto představují vytvořit omezení pro každou akci znalostní báze; To znamená, že kliknete na *vytvořit KB* nebo volání rozhraní API CreateKnowledgeBase.
* Maximální počet alternativních otázek na odpověď: 300
* Maximální počet adres URL: 10
* Maximální počet souborů: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizovat omezení volání znalostní báze
Představují limity pro každou akci aktualizace; To znamená, že kliknete na *uložit a jejich trénování* nebo volání rozhraní API UpdateKnowledgeBase.
* Délka názvu každého zdroje: 300
* Maximální počet přidaných nebo odstraněných alternativních otázek: 300
* Maximální počet polí metadat přidána nebo odstraněna: 10
* Maximální počet adres URL, které lze aktualizovat: 5

## <a name="next-steps"></a>Další kroky

Naučte se, kdy a jak změnit [cenové úrovně služby](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).
