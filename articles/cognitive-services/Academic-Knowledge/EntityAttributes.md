---
title: Atributy entit Academic Graph – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o atributech entity, která vám pomůže s využitím rozhraní Academic Graph rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f227cc03578adcfbf73fec3ae8941045e8352513
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182995"
---
# <a name="entity-attributes"></a>Atributy entit

Academic graph se skládá ze 7 typů entit. Všechny entity budou mít Entity ID a typ Entity.

## <a name="common-entity-attributes"></a>Běžné atributy entit
Name    |Popis                |Type       | Operace
------- | ------------------------- | --------- | ----------------------------
ID      |ID entity                  |Int64      |Rovná se
Ty      |Typ entity                |Výčet   |Rovná se

## <a name="entity-type-enum"></a>Entitu typu enum
Name                                                            |hodnota
----------------------------------------------------------------|-----
[Paper](PaperEntityAttributes.md)                               |0
[Author](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Konference řady](JournalEntityAttributes.md)                 |3
[Konference Instance](ConferenceInstanceEntityAttributes.md)    |4
[Affiliation](AffiliationEntityAttributes.md)                   |5
[Pole studie](FieldsOfStudyEntityAttributes.md)                      |6

