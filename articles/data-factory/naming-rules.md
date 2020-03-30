---
title: Pravidla pro pojmenování entit Azure Data Factory
description: Popisuje pravidla pojmenování entit Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 59cddf04493333b441dcf130d1d99d4fa946748c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837833"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – pravidla pojmenování
Následující tabulka obsahuje pravidla pojmenování artefaktů datové továrny.

| Name (Název) | Jedinečnost názvu | Kontroly ověření |
|:--- |:--- |:--- |
| Data Factory |Jedinečné v celém Microsoft Azure. Názvy jsou malá a velká `MyDF` `mydf` písmena, to znamená, a odkazují na stejnou datovou továrnu. |<ul><li>Každá datová továrna je vázaná přesně na jedno předplatné Azure.</li><li>Názvy objektů musí začínat písmenem nebo číslem a mohou obsahovat pouze písmena, číslice a znak pomlčky (-).</li><li>Každému znaku pomlčky (-) musí bezprostředně předcházet a musí za ním následovat písmeno nebo číslo. Po sobě jdoucí pomlčky nejsou v názvech kontejnerů povoleny.</li><li>Název může být dlouhý 3-63 znaků.</li></ul> |
| Propojené služby/datové sady/kanály |Unikátní s v datové továrně. Názvy nerozlišují malá a velká písmena. |<ul><li>Názvy objektů musí začínat písmenem, číslem nebo podtržítkem (_).</li><li>Následující znaky nejsou povoleny: ".", "+", "?", "/", "<", ">","*","%","&","","","&","",""""""""\\</li><li>Pomlčky ("-") nejsou povoleny v názvech propojených služeb a pouze datových sad.</li></ul>  |
| Skupina prostředků |Jedinečné v celém Microsoft Azure. Názvy nerozlišují malá a velká písmena. | Další informace najdete v tématu [Pravidla pro pojmenování Azure a omezení](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Další kroky
Zjistěte, jak vytvořit továrny na data podle podrobných pokynů v [úvodním článku: vytvoření článku datové továrny.](quickstart-create-data-factory-powershell.md) 
