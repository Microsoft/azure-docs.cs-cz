---
title: Atributy entity řady konference – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Přečtěte si o atributech, které můžete použít s entitou konferenčních řad.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: ecd8042212987849d9d302642f03e59493235599
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143910"
---
# <a name="conference-series-entity"></a>Entita konferenčních řad

> [!NOTE]
> Následující atributy jsou specifické pro entitu konferenčních řad. (Ty = "3")

Název | Popis | Typ | Operace
--- | --- | --- | ---
CC      |Celkový počet citací řady konferencí         |Datový typ Int32      |Žádný  
CN      |Normalizovaný název konferenční řady      |Řetězec     |Rovná se
DCN     |Zobrazovaný název řady konferencí         |Řetězec     |Žádný
ECC     |Počet odhadovaných citací v řadě konferencí   |Datový typ Int32      |Žádný
F.FId   |Pole ID entity studia přidružené k řadě konferencí |Int64  | Rovná se
F.FN    |Pole názvu studie přidružené k řadě konferencí  | Rovná<br/>StartsWith
ID      |ID entity                              |Int64      |Rovná se
PC    |Počet publikací celkem v řadě konferencí |Datový typ Int32 | Žádný
