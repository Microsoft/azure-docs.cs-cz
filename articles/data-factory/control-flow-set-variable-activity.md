---
title: Nastavení aktivity proměnných ve Službě Azure Data Factory
description: Přečtěte si, jak pomocí aktivity Nastavit proměnnou nastavit hodnotu nastavit hodnotu existující proměnné definované v kanálu datové továrny.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: 88500ecbc56b34551a0cbd3ca94727ba4bbcda9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930646"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Nastavení aktivity proměnných ve Službě Azure Data Factory

Aktivita Nastavit proměnnou slouží k nastavení hodnoty existující proměnné typu String, Bool nebo Array definované v kanálu Datové továrny.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název aktivity v kanálu | Ano
description | Text popisující, co aktivita dělá | ne
type | Typ aktivity je SetVariable | ano
value | Hodnota objektu literálu řetězce nebo výrazu použitá k nastavení zadané proměnné | ano
variableName | Název proměnné, která bude nastavena touto aktivitou | ano


## <a name="next-steps"></a>Další kroky
Informace o související aktivitě toku řízení podporované aplikací Data Factory: 

- [Aktivita připojení proměnné](control-flow-append-variable-activity.md)
