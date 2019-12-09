---
title: Nastavit aktivitu proměnné v Azure Data Factory
description: Naučte se používat aktivitu nastavit proměnnou k nastavení hodnoty existující proměnné definované v kanálu Data Factory.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930646"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Nastavit aktivitu proměnné v Azure Data Factory

Aktivitu nastavit proměnnou použijte k nastavení hodnoty existující proměnné typu String, bool nebo Array definovaného v kanálu Data Factory.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název aktivity v kanálu | Ano
description | Text popisující, co aktivita dělá | ne
type | Typ aktivity je SetVariable | ano
hodnota | Hodnota řetězcového literálu nebo objektu výrazu, která se používá k nastavení zadané proměnné | ano
variableName | Název proměnné, která bude nastavena touto aktivitou | ano


## <a name="next-steps"></a>Další kroky
Přečtěte si o související aktivitě toku ovládacích prvků, kterou podporuje Data Factory: 

- [Aktivita připojení proměnné](control-flow-append-variable-activity.md)
