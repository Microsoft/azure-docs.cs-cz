---
title: Meze a hranice - QnA Maker
description: QnA Maker má metalimity pro části znalostní báze a služby. Je důležité, aby vaše znalostní báze v rámci těchto limitů, aby bylo možné testovat a publikovat.
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273338"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limity a hranice znalostní báze QnA Maker

Limity QnA Maker uvedené níže jsou kombinací [limitů cenové úrovně Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) a [limitů cenové úrovně QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Chcete-li zjistit, kolik znalostních bází lze vytvořit pro jednotlivé zdroje a jak velké mohou každá znalostní báze růst, potřebujete znát obě sady limitů.

## <a name="knowledge-bases"></a>Znalostní báze

Maximální počet znalostních bází je založen na [limitech úrovně Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Úroveň kognitivního vyhledávání Azure** | **Zdarma** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximální povolený počet publikovaných znalostí|2|14|49|199|199|2,999|

 Například pokud vaše úroveň má 15 povolených indexů, můžete publikovat 14 znalostních bází (1 index na publikovanou znalostní bázi). Patnáctý index `testkb`, , se používá pro všechny znalostní báze pro vytváření a testování.

## <a name="extraction-limits"></a>Limity extrakce

### <a name="file-naming-constraints"></a>Omezení pro pojmenování souborů

Názvy souborů nesmí obsahovat následující znaky:

|Nepoužívejte znak|
|--|
|Jedna nabídka`'`|
|Dvojitá nabídka`"`|

### <a name="maximum-file-size"></a>Maximální velikost souboru

|Formát|Maximální velikost souboru (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximální počet souborů

Maximální počet souborů, které lze extrahovat, a maximální velikost souboru je založen na **[limitech cenové úrovně QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

### <a name="maximum-number-of-deep-links-from-url"></a>Maximální počet přímých odkazů z adresy URL

Maximální počet přímých odkazů, které lze procházet pro extrakci QnAs ze stránky URL, je **20**.

## <a name="metadata-limits"></a>Omezení metadat

Metadata jsou zobrazena jako textový klíč:hodnota `product:windows 10`dvojice, například . Je uložen a porovnán v případě malá písmena.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Podle cenové úrovně Azure Cognitive Search

Maximální počet polí metadat na znalostní bázi je založen na **[limitech úrovně Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)**.

|**Úroveň kognitivního vyhledávání Azure** | **Zdarma** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximální počet polí metadat na službu QnA Maker (napříč všemi kb)|1 000|100*|1 000|1 000|1 000|1 000|

### <a name="by-name-and-value"></a>Podle názvu a hodnoty

Délka a přijatelné znaky pro název metadat a hodnotu jsou uvedeny v následující tabulce.

|Položka|Povolené znaky|Shoda vzoru regex|Maximální počet znaků|
|--|--|--|--|
|Název (klíč)|Umožňuje<br>alfanumerické (písmena a číslice)<br>`_`(podtržítko)<br> Nesmí obsahovat mezery.|`^[a-zA-Z0-9_]+$`|100|
|Hodnota|Umožňuje vše kromě<br>`:`(dvojtečka)<br>`|`(svislá trubka)<br>Je povolena pouze jedna hodnota.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Omezení obsahu znalostní báze Knowledge Base
Celkové limity obsahu znalostní báze:
* Délka textu odpovědi: 25 000
* Délka textu otázky: 1 000
* Délka textu klíče/hodnoty metadat: 100
* Podporované znaky pro název metadat: Abecedy, číslice a`_`
* Podporované znaky pro hodnotu `:` metadat: Všechny kromě a`|`
* Délka názvu souboru: 200
* Podporované formáty souborů: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Maximální počet alternativních otázek: 300
* Maximální počet párů odpovědí na otázky: Závisí na zvolené **[úrovni Azure Cognitive Search.](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** Dvojice otázek a odpovědí se mapuje na dokument v indexu Azure Cognitive Search.
* STRÁNKA URL/HTML: 1 milion znaků

## <a name="create-knowledge-base-call-limits"></a>Vytvořit limity volání znalostní báze:
Ty představují limity pro každou akci vytvoření znalostní báze; to znamená, že klepnete na tlačítko *Vytvořit KB* nebo volání rozhraní CREATEKnowledgeBase API.
* Maximální počet alternativních otázek na odpověď: 300
* Maximální počet adres URL: 10
* Maximální počet souborů: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizace limitů volání znalostní báze Knowledge Base
Ty představují limity pro každou akci aktualizace; to znamená, že klepnete na tlačítko *Uložit a trénovat* nebo voláte rozhraní UpdateKnowledgeBase API.
* Délka každého zdrojového názvu: 300
* Maximální počet alternativních otázek, které byly přidány nebo odstraněny: 300
* Maximální počet přidaných nebo odstraněných polí metadat: 10
* Maximální počet adres URL, které lze aktualizovat: 5

## <a name="next-steps"></a>Další kroky

Přečtěte si, kdy a jak změnit [cenové úrovně služeb](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
