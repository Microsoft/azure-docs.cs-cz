---
title: Připojte aktivitu proměnnou ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak nastavit proměnnou připojit aktivitu přidat hodnotu existující proměnné pole definované v kanálech Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: douglasl
ms.openlocfilehash: e904075908fe7108c0566856b25fe03be0b7fd86
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54023791"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Připojte aktivitu proměnnou ve službě Azure Data Factory

Použije aktivitu přidat proměnnou přidat hodnotu existující proměnné pole definované v kanálech Data Factory.

## <a name="type-properties"></a>Typ vlastnosti

Vlastnost | Popis | Požaduje se
-------- | ----------- | --------
jméno | Název aktivity v kanálu | Ano
description | Text popisující, jakým způsobem aktivita naloží | ne
type | Typ aktivity je AppendVariable | ano
hodnota | Řetězcový literál nebo výraz hodnotu objektu použít k připojení do zadané proměnné | ano
NázevProměnné | Název proměnné, která bude upravena podle počtu aktivit, proměnná musí být typu "Pole" | ano

## <a name="next-steps"></a>Další postup
Další informace o aktivitu toku řízení související podporovaných službou Data Factory: 

- [Nastavte proměnné aktivity](control-flow-set-variable-activity.md)
