---
title: Připojit aktivitu proměnné v Azure Data Factory
description: Naučte se nastavit aktivitu připojit proměnnou pro přidání hodnoty do existující proměnné pole definované v kanálu Data Factory.
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5a9ed44e05c371460ae3ceab721f2236f6ec7fd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383402"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Připojit aktivitu proměnné v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Aktivitu připojit proměnnou použijte k přidání hodnoty do existující proměnné pole definované v kanálu Data Factory.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Vyžadováno
-------- | ----------- | --------
name | Název aktivity v kanálu | Yes
description | Text popisující, co aktivita dělá | ne
typ | Typ aktivity je AppendVariable | ano
hodnota | Hodnota řetězcového literálu nebo objektu výrazu, která se používá pro připojení k zadané proměnné | ano
variableName | Název proměnné, která bude upravena aktivitou, musí být proměnná typu Array. | ano

## <a name="next-steps"></a>Další kroky
Přečtěte si o související aktivitě toku ovládacích prvků, kterou podporuje Data Factory: 

- [Aktivita nastavení proměnné](control-flow-set-variable-activity.md)
