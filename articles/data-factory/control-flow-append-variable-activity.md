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
ms.openlocfilehash: 5d74cd0fcd524f00d79eb3fbab386c602a413766
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81414209"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Připojit aktivitu proměnné v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Aktivitu připojit proměnnou použijte k přidání hodnoty do existující proměnné pole definované v kanálu Data Factory.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Vyžadováno
-------- | ----------- | --------
name | Název aktivity v kanálu | Ano
description | Text popisující, co aktivita dělá | ne
typ | Typ aktivity je AppendVariable | ano
hodnota | Hodnota řetězcového literálu nebo objektu výrazu, která se používá pro připojení k zadané proměnné | ano
variableName | Název proměnné, která bude upravena aktivitou, musí být proměnná typu Array. | ano

## <a name="next-steps"></a>Další kroky
Přečtěte si o související aktivitě toku ovládacích prvků, kterou podporuje Data Factory: 

- [Aktivita nastavení proměnné](control-flow-set-variable-activity.md)
