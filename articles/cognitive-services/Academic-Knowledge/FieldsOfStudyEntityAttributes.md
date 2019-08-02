---
title: Pole atributů entit studie – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Seznamte se s atributy, které můžete použít spolu s polem studijní entity v Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d54f3c72462e6702b09068092b7c18ea50f12048
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704993"
---
# <a name="field-of-study-entity"></a>Pole studijní entity

<sub>* Následující atributy jsou specifické pro pole entity studie. (Ty = 6 ')</sub>

Name    |Popis                            |type       | Operace
------- | ------------------------------------- | --------- | ----------------------------
Id      |ID entity                              |Int64      |Je rovno
FN      |Pole normalizovaného názvu studie         |Řetězec     |Je rovno
DFN     |Pole zobrazovaného názvu studie            |Řetězec     |žádný
CC      |Pole počtu citací celkem v studiu    |Int32      |žádný  
ECC     |Pole celkového počtu odhadovaných citací|Int32      |žádný
FL      |Úroveň v oblastech hierarchie studie     |Int32      |Rovná <br/>Mezi
FP.FN   |Nadřazené pole s názvem studie             |Řetězec     |Je rovno
FP.FId  |Nadřazené pole s ID studie               |Int64      |Je rovno
FC. VISTASCAN   |Podřízené pole s názvem studie              |Řetězec     |Je rovno
FC. Ukazující  |Podřízené pole ID studie                |Int64      |Je rovno
