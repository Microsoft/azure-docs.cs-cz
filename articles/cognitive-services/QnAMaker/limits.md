---
title: Omezení a hranice – QnA Maker
description: QnA Maker má meta omezení pro části znalostní báze a služby. Pro účely testování a publikování je důležité zachovat znalostní bázi v rámci těchto omezení.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 1a384eed542d0c09a973e0e68288f9fc1660cc96
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380893"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker limity a hranice znalostní báze

Níže uvedené limity QnA Maker jsou kombinací [omezení cenové úrovně Azure kognitivní hledání](../../search/search-limits-quotas-capacity.md) a [QnA maker limity cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Je potřeba znát obě sady omezení, abyste porozuměli tomu, kolik znalostní báze můžete vytvořit podle jednotlivých prostředků a jak velký může růst každý znalostní báze.

## <a name="knowledge-bases"></a>Znalostní báze

Maximální počet základů znalostní báze je založený na [omezeních vrstvy Azure kognitivní hledání](../../search/search-limits-quotas-capacity.md).

|**Úroveň Kognitivní hledání Azure** | Zadejte možnost pro **bezplatnou** SKU. | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximální povolený počet publikovaných základů znalostí|2|14|49|199|199|2 999|

 Pokud má vaše úroveň například 15 povolených indexů, můžete publikovat 14 báze znalostí (1 index na publikovanou znalostní bázi). Patnáctý index se `testkb` používá pro všechny znalostní báze pro vytváření a testování.

## <a name="extraction-limits"></a>Omezení extrakce

### <a name="file-naming-constraints"></a>Omezení pojmenovávání souborů

Názvy souborů nesmí obsahovat tyto znaky:

|Nepoužívat znak|
|--|
|Jednoduchá uvozovka `'`|
|Dvojité uvozovky `"`|

### <a name="maximum-file-size"></a>Maximální velikost souboru

|Formát|Maximální velikost souboru (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximální počet souborů

Maximální počet souborů, které se dají extrahovat a maximální velikost souboru, je založený na vašich **[QnA maker omezeních cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

> [!NOTE]
> QnA Maker Managed (Preview) je bezplatná služba bez omezení počtu zdrojů, které se dají přidat. Propustnost se momentálně omezené za 10 transakcí za sekundu pro rozhraní API pro správu i pro předpovědi rozhraní API.

### <a name="maximum-number-of-deep-links-from-url"></a>Maximální počet přímých odkazů z adresy URL

Maximální počet přímých odkazů, které lze procházet pro extrakci QnAs ze stránky adresy URL, je **20**.

## <a name="metadata-limits"></a>Omezení metadat

Metadata jsou uvedena jako textový klíč: dvojice hodnot, jako je například `product:windows 10` . Je uložen a porovnán v malých malých písmenech.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Podle cenové úrovně Azure Kognitivní hledání

Maximální počet polí metadat na bázi znalostní báze je založený na **[omezeních vaší vrstvy Azure kognitivní hledání](../../search/search-limits-quotas-capacity.md)**.

|**Úroveň Kognitivní hledání Azure** | Zadejte možnost pro **bezplatnou** SKU. | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximální počet polí metadat na službu QnA Maker (napříč všemi aktualizací KB)|1 000|100 *|1 000|1 000|1 000|1 000|

### <a name="by-name-and-value"></a>Podle názvu a hodnoty

Délka a přijatelné znaky pro název a hodnotu metadat jsou uvedeny v následující tabulce.

|Položka|Povolené znaky|Porovnávání vzorů regulárního výrazu|Maximální počet znaků|
|--|--|--|--|
|Název (klíč)|Poskytuje<br>alfanumerické znaky (písmena a číslice)<br>`_` podtržítkem<br> Nesmí obsahovat mezery.|`^[a-zA-Z0-9_]+$`|100|
|Hodnota|Umožňuje vše kromě<br>`:` kolon<br>`|` (vertikální svislá čára)<br>Je povolena pouze jedna hodnota.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Omezení obsahu znalostní báze
Celková omezení obsahu ve znalostní bázi Knowledge Base:
* Délka textu odpovědi: 25 000
* Délka textu otázky: 1 000
* Délka textu klíče metadat: 100
* Délka textu hodnoty metadat: 500
* Podporované znaky pro název metadat: abecedy, číslice a `_`
* Podporované znaky pro hodnotu metadat: vše kromě `:` a `|`
* Délka názvu souboru: 200
* Podporované formáty souborů: ". TSV", ". PDF", ". txt", ". docx", ". xlsx".
* Maximální počet alternativních otázek: 300
* Maximální počet párů otázek a odpovědí: závisí na zvolené **[úrovni Azure kognitivní hledání](../../search/search-limits-quotas-capacity.md#document-limits)** . Pár otázek a odpovědí se mapuje na dokument v Azure Kognitivní hledání indexu.
* Adresa URL/stránka HTML: 1 000 000 znaků

## <a name="create-knowledge-base-call-limits"></a>Vytvořit limity volání ve znalostní bázi Knowledge Base:
Tyto hodnoty udávají omezení pro každou akci vytvoření znalostní báze. To znamená, že kliknete na *vytvořit KB* nebo zavolat rozhraní CreateKnowledgeBase API.
* Doporučený maximální počet alternativních otázek na odpověď: 300
* Maximální počet adres URL: 10
* Maximální počet souborů: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizovat omezení volání znalostní báze
Tyto hodnoty udávají omezení pro každou akci aktualizace. To znamená, že kliknete na *Uložit a naučíte* se volat rozhraní UpdateKnowledgeBase API.
* Délka každého názvu zdroje: 300
* Doporučený maximální počet přidaných nebo odstraněných alternativních otázek: 300
* Maximální počet přidaných nebo odstraněných polí metadat: 10
* Maximální počet adres URL, které lze aktualizovat: 5

## <a name="next-steps"></a>Další kroky

Naučte se, kdy a jak změnit [cenové úrovně služby](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
