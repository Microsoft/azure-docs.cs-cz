---
title: Nastavte proměnné aktivity ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak používat aktivitu nastavit proměnnou nastavíte hodnotu existující proměnné definované v kanálech Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: ff9bfce1f9262d78ba17abdd88c481d5057d5f38
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076415"
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
NázevProměnné | Název proměnné, která stanoví tato aktivita | ano


## <a name="next-steps"></a>Další postup
Další informace o aktivitu toku řízení související podporovaných službou Data Factory: 

- [Připojte aktivitu proměnnou](control-flow-append-variable-activity.md)
