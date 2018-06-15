---
title: Omezení QnA Maker - kognitivní služby Azure | Microsoft Docs
description: Omezení QnA Maker
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 4d2bafad08ffab76743cb802733a5d2f01a97f06
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "35343905"
---
# <a name="qna-maker-limits"></a>Omezení QnA Maker
Úplný seznam omezení napříč QnA Maker.

## <a name="knowledge-bases"></a>Znalostních bází

* Maximální počet znalostních bází na základě [omezení vrstvy Azure Search](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Azure Search vrstvy** | **Volné** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|(Maximální počet indexů – 1 (vyhrazené pro test) povolen maximální počet publikovaných znalostních bází|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>Extrakce omezení
* Maximální počet souborů, které lze extrahovat a maximální velikost souboru: viz [QnAMaker ceny](https://azure.microsoft.com/en-in/pricing/details/cognitive-services/qna-maker/)
* Maximální počet přímým odkazy, které můžete procházet pro extrakci QnAs ze stránky HTML – nejčastější dotazy: 20

## <a name="metadata-limits"></a>Omezení metadat
* Maximální počet polí metadata na znalostní bázi knowledge base na základě [omezení vrstvy Azure Search](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Azure Search vrstvy** | **Volné** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Pole maximální metadat pro službu QnA Maker (v rámci všechny články znalostní báze)|1000|100 *|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>Omezení obsahu znalostní báze Knowledge Base
Celkové omezení pro obsah znalostní báze knowledge base:
* Délka textu odpovědi: částku 250 000 jedné
* Délka textu otázku: 1000
* Délka textu klíč/hodnota metadat: 100
* Podporované znaků pro název metadat: písmena, číslice _  
* Znaky podporované pro hodnota metadat: všechny kromě: a | 
* Délka názvu souboru: 200
* Podporované formáty souborů: "TSV", ".pdf", ".txt", ".docx", "XLSX".
* Maximální počet alternativní otázek: 100
* Maximální počet dvojic otázku odpovědí: závisí na [Azure Search vrstvy](https://docs.microsoft.com/en-in/azure/search/search-limits-quotas-capacity#document-limits) vybrali 

## <a name="create-knowledge-base-call-limits"></a>Vytvořte omezení volání znalostní báze Knowledge base:
Tyto představují limity pro každý vytvořit znalostní báze knowledge base akce; To znamená, že kliknete na *vytvořit KB* nebo volání rozhraní API CreateKnowledgeBase.
* Maximální počet alternativní dotazy na odpovědí: 100
* Maximální počet adres URL: 10
* Maximální počet souborů: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizovat limity volání znalostní báze Knowledge base
Tyto představují limity pro každou akci aktualizace; To znamená, že kliknete na *uložte a cvičení* nebo volání rozhraní API UpdateKnowledgeBase.
* Délka názvu každého zdroje: 300
* Maximální počet otázek alternativní otázky přidány nebo odstraněny: 100
* Maximální počet polí metadata přidány nebo odstraněny: 10
* Maximální počet adres URL, které lze aktualizovat: 5
