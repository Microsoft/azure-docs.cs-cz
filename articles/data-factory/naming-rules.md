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
ms.openlocfilehash: f922ada663391cf65a61f4e18bba53668f9c4a1a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419404"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – pravidla pojmenování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Následující tabulka obsahuje pravidla pojmenování artefaktů datové továrny.

| Název | Jedinečnost názvu | Kontroly ověření |
|:--- |:--- |:--- |
| Data Factory |Jedinečné v celém Microsoft Azure. Názvy jsou malá a velká `MyDF` `mydf` písmena, to znamená, a odkazují na stejnou datovou továrnu. |<ul><li>Každá datová továrna je vázaná přesně na jedno předplatné Azure.</li><li>Názvy objektů musí začínat písmenem nebo číslem a mohou obsahovat pouze písmena, číslice a znak pomlčky (-).</li><li>Každému znaku pomlčky (-) musí bezprostředně předcházet a musí za ním následovat písmeno nebo číslo. Po sobě jdoucí pomlčky nejsou v názvech kontejnerů povoleny.</li><li>Název může být dlouhý 3-63 znaků.</li></ul> |
| Propojené služby/datové sady/kanály |Unikátní s v datové továrně. Názvy nerozlišují malá a velká písmena. |<ul><li>Názvy objektů musí začínat písmenem, číslem nebo podtržítkem (_).</li><li>Následující znaky nejsou povoleny: ".", "+", "?", "/", "<", ">","*","%","&","","","&","",""""""""\\</li><li>Pomlčky ("-") nejsou povoleny v názvech propojených služeb a pouze datových sad.</li></ul>  |
| Skupina prostředků |Jedinečné v celém Microsoft Azure. Názvy nerozlišují malá a velká písmena. | Další informace najdete v tématu [Pravidla pro pojmenování Azure a omezení](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Další kroky
Zjistěte, jak vytvořit továrny na data podle podrobných pokynů v [úvodním článku: vytvoření článku datové továrny.](quickstart-create-data-factory-powershell.md) 
