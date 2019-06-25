---
title: Atributy entit Academic Graph – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o atributech entity, která vám pomůže s využitím rozhraní Academic Graph rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340175"
---
# <a name="entity-attributes"></a>Atributy entit

Academic graph se skládá ze 7 typů entit. Všechny entity budou mít Entity ID a typ Entity.

## <a name="common-entity-attributes"></a>Běžné atributy entit
Name    |Popis                |Type       | Operace
------- | ------------------------- | --------- | ----------------------------
ID      |Entity ID                  |Int64      |Je rovno
Ty      |Typ entity                |Výčet   |Je rovno

## <a name="entity-type-enum"></a>Entitu typu enum
Name                                                            |value
----------------------------------------------------------------|-----
[Paper](PaperEntityAttributes.md)                               |0
[Author](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Konference řady](JournalEntityAttributes.md)                 |3
[Konference Instance](ConferenceInstanceEntityAttributes.md)    |4
[Affiliation](AffiliationEntityAttributes.md)                   |5
[Pole studie](FieldsOfStudyEntityAttributes.md)                      |6

