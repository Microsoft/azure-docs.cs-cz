---
title: Připojit aktivitu proměnné v Azure Data Factory
description: Naučte se nastavit aktivitu připojit proměnnou pro přidání hodnoty do existující proměnné pole definované v kanálu Data Factory.
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
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679979"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Připojit aktivitu proměnné v Azure Data Factory

Aktivitu připojit proměnnou použijte k přidání hodnoty do existující proměnné pole definované v kanálu Data Factory.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název aktivity v kanálu | Ano
description | Text popisující, co aktivita dělá | ne
type | Typ aktivity je AppendVariable | ano
hodnota | Hodnota řetězcového literálu nebo objektu výrazu, která se používá pro připojení k zadané proměnné | ano
variableName | Název proměnné, která bude upravena aktivitou, musí být proměnná typu Array. | ano

## <a name="next-steps"></a>Další kroky
Přečtěte si o související aktivitě toku ovládacích prvků, kterou podporuje Data Factory: 

- [Aktivita nastavení proměnné](control-flow-set-variable-activity.md)
