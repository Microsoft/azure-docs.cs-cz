---
title: Pravidla pro pojmenování entit Azure Data Factory
description: Popisuje pravidla pojmenování entit Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8fa1340b586434bf98d51437d4dc6b08594f0afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931891"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – pravidla pojmenování
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte [si téma pravidla pojmenování v datové továrně](../naming-rules.md).

Následující tabulka obsahuje pravidla pojmenování artefaktů datové továrny.

| Name (Název) | Jedinečnost názvu | Kontroly ověření |
|:--- |:--- |:--- |
| Data Factory |Jedinečné v celém Microsoft Azure. Názvy jsou malá a velká `MyDF` `mydf` písmena, to znamená, a odkazují na stejnou datovou továrnu. |<ul><li>Každá datová továrna je vázaná přesně na jedno předplatné Azure.</li><li>Názvy objektů musí začínat písmenem nebo číslem a mohou obsahovat pouze písmena, číslice a znak pomlčky (-).</li><li>Každému znaku pomlčky (-) musí bezprostředně předcházet a musí za ním následovat písmeno nebo číslo. Po sobě jdoucí pomlčky nejsou v názvech kontejnerů povoleny.</li><li>Název může být dlouhý 3-63 znaků.</li></ul> |
| Propojené služby/tabulky/kanály |Unikátní s v datové továrně. Názvy nerozlišují malá a velká písmena. |<ul><li>Maximální počet znaků v názvu tabulky: 260.</li><li>Názvy objektů musí začínat písmenem, číslem nebo podtržítkem (_).</li><li>Následující znaky nejsou povoleny: ".", "+", "?", "/", "<", ">","*","%","&","","","&","",""""""""\\</li></ul> |
| Skupina prostředků |Jedinečné v celém Microsoft Azure. Názvy nerozlišují malá a velká písmena. |<ul><li>Maximální počet znaků: 1000.</li><li>Název může obsahovat písmena, číslice a následující znaky: "-", "_", "" a "."</li></ul> |

