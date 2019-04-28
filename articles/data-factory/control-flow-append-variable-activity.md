---
title: Připojte aktivitu proměnnou ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak nastavit proměnnou připojit aktivitu přidat hodnotu existující proměnné pole definované v kanálech Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: a5efe946000eb00e65d314ae53d7136761e2109d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557218"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Připojte aktivitu proměnnou ve službě Azure Data Factory

Použije aktivitu přidat proměnnou přidat hodnotu existující proměnné pole definované v kanálech Data Factory.

## <a name="type-properties"></a>Typ vlastnosti

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název aktivity v kanálu | Ano
description | Text popisující, jakým způsobem aktivita naloží | ne
type | Typ aktivity je AppendVariable | ano
value | Řetězcový literál nebo výraz hodnotu objektu použít k připojení do zadané proměnné | ano
variableName | Název proměnné, která bude upravena podle počtu aktivit, proměnná musí být typu "Pole" | ano

## <a name="next-steps"></a>Další postup
Další informace o aktivitu toku řízení související podporovaných službou Data Factory: 

- [Nastavte proměnné aktivity](control-flow-set-variable-activity.md)
