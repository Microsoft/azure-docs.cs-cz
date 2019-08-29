---
title: Pravidla pro pojmenování Azure Data Factory entit | Microsoft Docs
description: Popisuje pravidla vytváření názvů pro Entity Data Factory.
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
ms.openlocfilehash: 4397f7efdb9ff797375584f5a43af2833a6a2989
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140992"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – pravidla pojmenování
Následující tabulka poskytuje pravidla pro pojmenování artefaktů Data Factory.

| Name | Jedinečnost názvu | Kontroly ověřování |
|:--- |:--- |:--- |
| Data Factory |Jedinečné v rámci Microsoft Azure. V názvech se nerozlišují malá `MyDF` a velká písmena, to znamená a `mydf` odkazují na stejný objekt pro vytváření dat. |<ul><li>Každá datová továrna je vázaná na právě jedno předplatné Azure.</li><li>Název objektu musí začínat písmenem nebo číslicí a může obsahovat jenom písmena, číslice a spojovníky (-).</li><li>Každý znak spojovníku (-) musí bezprostředně předcházet a musí následovat písmeno nebo číslo. Po sobě jdoucí pomlčky nejsou v názvech kontejnerů povolené.</li><li>Název může být 3-63 znaků dlouhý.</li></ul> |
| Propojené služby/datové sady/kanály |Jedinečné v objektu pro vytváření dat. V názvech se nerozlišují malá a velká písmena. |<ul><li>Názvy objektů musí začínat písmenem, číslicí nebo podtržítkem (_).</li><li>Nejsou povoleny následující znaky: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li><li>Pomlčky ("-") nejsou povoleny v názvech propojených služeb a datových sad.</li></ul>  |
| Skupina prostředků |Jedinečné v rámci Microsoft Azure. V názvech se nerozlišují malá a velká písmena. | Další informace najdete v tématu [pravidla a omezení pro pojmenovávání Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Další postup
Naučte se vytvářet datové továrny podle podrobných pokynů v tématu [rychlý Start: vytvoření datové továrny](quickstart-create-data-factory-powershell.md) . 
