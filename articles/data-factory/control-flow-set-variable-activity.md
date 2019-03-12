---
title: Nastavte proměnné aktivity ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak používat aktivitu nastavit proměnnou nastavíte hodnotu existující proměnné definované v kanálech Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 71abfdff629f36b278488851b546c7371353a4d9
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575286"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Nastavte proměnné aktivity ve službě Azure Data Factory

Použije aktivitu nastavit proměnnou nastavíte hodnotu existující proměnné typu String, Bool nebo pole definované v kanálech Data Factory.

## <a name="type-properties"></a>Typ vlastnosti

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název aktivity v kanálu | Ano
description | Text popisující, jakým způsobem aktivita naloží | ne
type | Typ aktivity je SetVariable | ano
hodnota | Řetězcový literál nebo výraz hodnotu objektu používá k nastavení zadané proměnné | ano
variableName | Název proměnné, která stanoví tato aktivita | ano


## <a name="next-steps"></a>Další postup
Další informace o aktivitu toku řízení související podporovaných službou Data Factory: 

- [Připojte aktivitu proměnnou](control-flow-append-variable-activity.md)
