---
title: Atributy entit akademického grafu – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Přečtěte si o atributech entit, které můžete použít se akademickým grafem v Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705017"
---
# <a name="entity-attributes"></a>Atributy entity

Akademický graf se skládá z 7 typů entit. Všechny entity budou mít ID entity a typ entity.

## <a name="common-entity-attributes"></a>Společné atributy entit
Name    |Popis                |type       | Operace
------- | ------------------------- | --------- | ----------------------------
Id      |ID entity                  |Int64      |Je rovno
Ty      |Typ entity                |Výčet   |Je rovno

## <a name="entity-type-enum"></a>Výčet typu entity
Name                                                            |value
----------------------------------------------------------------|-----
[Paper](PaperEntityAttributes.md)                               |0
[Author](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Série konferencí](JournalEntityAttributes.md)                 |3
[Instance konference](ConferenceInstanceEntityAttributes.md)    |4
[Affiliation](AffiliationEntityAttributes.md)                   |5
[Polní studie](FieldsOfStudyEntityAttributes.md)                      |6

