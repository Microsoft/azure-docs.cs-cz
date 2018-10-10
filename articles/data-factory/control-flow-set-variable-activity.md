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
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: eed589cd78211cfe3955f591ce1f165e989b1308
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48904129"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Nastavte proměnné aktivity ve službě Azure Data Factory

Použije aktivitu nastavit proměnnou nastavíte hodnotu existující proměnné definované v kanálech Data Factory.

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
