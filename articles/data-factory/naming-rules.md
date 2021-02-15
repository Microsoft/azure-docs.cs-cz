---
title: Pravidla pro pojmenování Azure Data Factory entit
description: Popisuje pravidla vytváření názvů pro Entity Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f0d14760ce3e6403c9b6fe8cc7a2100aeb3f39a6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372905"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – pravidla pojmenování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Následující tabulka poskytuje pravidla pro pojmenování artefaktů Data Factory.

| Name | Jedinečnost názvu | Kontroly ověřování |
|:--- |:--- |:--- |
| Datová továrna | Jedinečné v rámci Microsoft Azure. V názvech se nerozlišují malá a velká písmena, to znamená `MyDF` a `mydf` odkazují na stejný objekt pro vytváření dat. |<ul><li>Každá datová továrna je vázaná na právě jedno předplatné Azure.</li><li>Název objektu musí začínat písmenem nebo číslicí a může obsahovat jenom písmena, číslice a spojovníky (-).</li><li>Každý znak spojovníku (-) musí bezprostředně předcházet a musí následovat písmeno nebo číslo. Po sobě jdoucí pomlčky nejsou v názvech kontejnerů povolené.</li><li>Název může být 3-63 znaků dlouhý.</li></ul> |
| Propojené služby/datové sady/kanály/toky dat | Jedinečné v rámci objektu pro vytváření dat. V názvech se nerozlišují malá a velká písmena. |<ul><li>Názvy objektů musí začínat písmenem.</li><li>Následující znaky nejsou povoleny: ".", "+", "?", "/", "<", ">", "*", "%", "&" \\ , ":", ""</li><li>Pomlčky ("-") nejsou povoleny v názvech propojených služeb, datových toků a datových sad.</li></ul>  |
| Integration Runtime |Jedinečné v rámci objektu pro vytváření dat. V názvech se nerozlišují malá a velká písmena. |<ul><li>Název prostředí Integration runtime může obsahovat jenom písmena, číslice a znak spojovníku (-).</li><li>První a poslední znak musí být písmeno nebo číslo. Každý znak spojovníku (-) musí bezprostředně předcházet a musí následovat písmeno nebo číslo.</li><li>Po sobě jdoucí pomlčky nejsou v názvu modulu runtime integrace povoleny. </li></ul> |
| Transformace toku dat | Jedinečné v rámci toku dat. V názvech se nerozlišují malá a velká písmena. | <ul><li>Názvy transformace toku dat můžou obsahovat jenom písmena a číslice.</li><li>Prvním znakem musí být písmeno. </li></ul> |
| Skupina prostředků |Jedinečné v rámci Microsoft Azure. V názvech se nerozlišují malá a velká písmena. | Další informace najdete v tématu [pravidla a omezení pro pojmenovávání Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Další kroky
Naučte se vytvářet datové továrny podle podrobných pokynů v tématu [rychlý Start: vytvoření datové továrny](quickstart-create-data-factory-powershell.md) . 
