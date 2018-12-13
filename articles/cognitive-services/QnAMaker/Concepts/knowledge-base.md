---
title: Znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Znalostní báze QnA Maker se skládá ze sady páry otázek a odpovědí (QnA) a volitelná metadata spojená s každou dvojici QnA.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 865525d9c978f3168a4c580dec20f0f91f0a0d16
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074070"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Co je znalostní bázi nástroje QnA Maker?

Znalostní báze QnA Maker se skládá ze sady páry otázek a odpovědí (QnA) a volitelná metadata spojená s každou dvojici QnA.

## <a name="key-knowledge-base-concepts"></a>Koncepty klíče znalostní báze

* **Dotazy** -dotaz obsahuje text, který nejlépe představuje uživatelský dotaz. 
* **Odpovědi** – odpověď je tato odpověď, která je vrácena, pokud uživatelský dotaz je nalezena shoda s přidružené otázku.  
* **Metadata** – Metadata jsou značky přiřazeny k pár QnA a jsou reprezentovány jako páry klíč hodnota. Metadata se používají k filtrování QnA páry a omezit tak skupinu, přes který dotaz se provádí porovnávání.

Jeden QnA, reprezentovaný číselné ID QnA má několik variant otázky (alternativní dotazy), že všechny mapovat na jednu odpověď. Každý takový pár kromě toho může mít více polí metadat s ním spojená.

![Nástroj QnA Maker znalostních bází](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formát obsahu znalostní báze

Při ingestování formátovaný obsah do znalostní báze, nástroj QnA Maker se pokusí převést obsah na markdown. Čtení [to](https://aka.ms/qnamaker-docs-markdown-support) blogu o markdownu formáty používaném většina klientů konverzace.

Pole metadat se skládá z dvojice klíč hodnota oddělené dvojtečkou **(produktu: Shredder)**. Klíče a hodnoty musí být prostého textu. Klíč metadat nesmí obsahovat žádné mezery.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Životní cyklus vývoje znalostní báze](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Další informace najdete v tématech

[Přehled služby QnA Maker](../Overview/overview.md)