---
title: Nastavit aktivitu proměnné v Azure Data Factory | Microsoft Docs
description: Naučte se používat aktivitu nastavit proměnnou k nastavení hodnoty existující proměnné definované v kanálu Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.openlocfilehash: cfe6dd63234a7750fe01614d6f1b38bb7cce1adb
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142447"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Nastavit aktivitu proměnné v Azure Data Factory

Aktivitu nastavit proměnnou použijte k nastavení hodnoty existující proměnné typu String, bool nebo Array definovaného v kanálu Data Factory.

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Požadováno
-------- | ----------- | --------
name | Název aktivity v kanálu | Ano
description | Text popisující, co aktivita dělá | ne
type | Typ aktivity je SetVariable | ano
value | Hodnota řetězcového literálu nebo objektu výrazu, která se používá k nastavení zadané proměnné | ano
variableName | Název proměnné, která bude nastavena touto aktivitou | ano


## <a name="next-steps"></a>Další postup
Přečtěte si o související aktivitě toku ovládacích prvků, kterou podporuje Data Factory: 

- [Aktivita připojení proměnné](control-flow-append-variable-activity.md)
