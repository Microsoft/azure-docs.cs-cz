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
ms.date: 12/18/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 62478eed2eb647a2f29e488e60e41dd9ae501273
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605836"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Co je znalostní bázi nástroje QnA Maker?

Znalostní báze QnA Maker se skládá ze sady páry otázek a odpovědí (QnA) a volitelná metadata spojená s každou dvojici QnA.

## <a name="key-knowledge-base-concepts"></a>Koncepty klíče znalostní báze

* **Dotazy** -dotaz obsahuje text, který nejlépe představuje uživatelský dotaz. 
* **Odpovědi** – odpověď je tato odpověď, která je vrácena, pokud uživatelský dotaz je nalezena shoda s přidružené otázku.  
* **Metadata** – Metadata jsou značky přiřazeny k pár QnA a jsou reprezentovány jako páry klíč hodnota. Metadata značky se používají k filtrování QnA páry a omezit tak skupinu, přes který dotaz se provádí porovnávání.

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