---
title: Pravidla pro pojmenování Azure Data Factory entit – verze 1
description: Popisuje pravidla pojmenování pro Entity Data Factory v1.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0ef997f54deafc56f1b3af603071c0e650be40d4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495391"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Pravidla pro pojmenování Azure Data Factory entit
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [pravidla pojmenování v Data Factory](../naming-rules.md).

Následující tabulka poskytuje pravidla pro pojmenování artefaktů Data Factory.

| Name | Jedinečnost názvu | Kontroly ověřování |
|:--- |:--- |:--- |
| Data Factory |Jedinečné v rámci Microsoft Azure. V názvech se nerozlišují malá a velká písmena, to znamená `MyDF` a `mydf` odkazují na stejný objekt pro vytváření dat. |<ul><li>Každá datová továrna je vázaná na právě jedno předplatné Azure.</li><li>Název objektu musí začínat písmenem nebo číslicí a může obsahovat jenom písmena, číslice a spojovníky (-).</li><li>Každý znak spojovníku (-) musí bezprostředně předcházet a musí následovat písmeno nebo číslo. Po sobě jdoucí pomlčky nejsou v názvech kontejnerů povolené.</li><li>Název může být 3-63 znaků dlouhý.</li></ul> |
| Propojené služby, tabulky a kanály |Jedinečné v objektu pro vytváření dat. V názvech se nerozlišují malá a velká písmena. |<ul><li>Maximální počet znaků v názvu tabulky: 260.</li><li>Názvy objektů musí začínat písmenem, číslicí nebo podtržítkem (_).</li><li>Nejsou povoleny následující znaky: ".", "+", "?", "/", "<", ">", "*", "%", "&" \\ , ":", ""</li></ul> |
| Skupina prostředků |Jedinečné v rámci Microsoft Azure. V názvech se nerozlišují malá a velká písmena. |<ul><li>Maximální počet znaků: 1000.</li><li>Název může obsahovat písmena, číslice a následující znaky: "-", "_", "," a "."</li></ul> |

