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
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: douglasl
ms.openlocfilehash: cc573028779bcd6b77394bbeefbea58f714b835c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017340"
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
