---
title: Připojit aktivitu proměnné v Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: 24808c9ed4bda5e8d3b0ce23ab93bc59eb260374
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141726"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Připojit aktivitu proměnné v Azure Data Factory

Aktivitu připojit proměnnou použijte k přidání hodnoty do existující proměnné pole definované v kanálu Data Factory.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Požadováno
-------- | ----------- | --------
name | Název aktivity v kanálu | Ano
description | Text popisující, co aktivita dělá | ne
type | Typ aktivity je AppendVariable | ano
value | Hodnota řetězcového literálu nebo objektu výrazu, která se používá pro připojení k zadané proměnné | ano
variableName | Název proměnné, která bude upravena aktivitou, musí být proměnná typu Array. | ano

## <a name="next-steps"></a>Další postup
Přečtěte si o související aktivitě toku ovládacích prvků, kterou podporuje Data Factory: 

- [Aktivita nastavení proměnné](control-flow-set-variable-activity.md)
