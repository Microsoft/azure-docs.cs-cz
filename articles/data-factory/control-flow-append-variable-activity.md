---
title: Připojit proměnnou aktivitu v Azure Data Factory
description: Zjistěte, jak nastavit aktivitu Připojit proměnnou pro přidání hodnoty k existující proměnné pole definované v kanálu Datové továrny.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: bcecd3411b6e6e751032f33862a671d8adbca046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679979"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Připojit proměnnou aktivitu v Azure Data Factory

Pomocí aktivity Připojit proměnnou můžete přidat hodnotu k existující proměnné pole definované v kanálu datové továrny.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název aktivity v kanálu | Ano
description | Text popisující, co aktivita dělá | ne
type | Typ aktivity je AppendVariable | ano
value | Hodnota objektu literálu řetězce nebo výrazu použitá k připojení k zadané proměnné | ano
variableName | Název proměnné, která bude změněna podle aktivity, musí být proměnná typu Array | ano

## <a name="next-steps"></a>Další kroky
Informace o související aktivitě toku řízení podporované aplikací Data Factory: 

- [Aktivita nastavení proměnné](control-flow-set-variable-activity.md)
