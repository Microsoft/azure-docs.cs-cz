---
title: Atributy entity řady konference – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Přečtěte si o atributech, které můžete použít s entitou konferenčních řad.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: feed324202f6a75ceb7e9089875b899c51cd8ae6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705047"
---
# <a name="conference-series-entity"></a>Entita konferenčních řad

<sub>* Následující atributy jsou specifické pro entitu konferenčních řad. (Ty = "3")</sub>

Name    |Popis                            |type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID entity                              |Int64      |Je rovno
CN      |Normalizovaný název konferenční řady      |Řetězec     |Je rovno
DCN     |Zobrazovaný název řady konferencí         |Řetězec     |žádný
CC      |Celkový počet citací řady konferencí         |Int32      |žádný  
ECC     |Počet odhadovaných citací v řadě konferencí   |Int32      |žádný
F.FId   |Pole ID entity studia přidružené k řadě konferencí |Int64  | Je rovno
F.FN    |Pole názvu studie přidružené k řadě konferencí  | Rovná<br/>StartsWith
