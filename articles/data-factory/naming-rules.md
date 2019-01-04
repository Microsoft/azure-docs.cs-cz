---
title: Pravidla pro pojmenovávání entity služby Azure Data Factory | Dokumentace Microsoftu
description: Popisuje pravidla pojmenování pro entity služby Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: c02a9393de72b827b7e38b52d06589f042d581b0
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024769"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – pravidla pojmenování
Následující tabulka obsahuje pravidla pojmenování artefaktů služby Data Factory.

| Název | Jedinečnost názvu | Ověřovací kontroly |
|:--- |:--- |:--- |
| Data Factory |Jedinečný v rámci Microsoft Azure. Názvy jsou malá a velká písmena, tedy `MyDF` a `mydf` odkazovat na stejnou datovou továrnou. |<ul><li>Každá datová továrna se váže na přesně jedno předplatné Azure.</li><li>Názvy objektů musí začínat písmenem nebo číslicí a může obsahovat jenom písmena, číslice a znak spojovníku (-).</li><li>Každému znaku pomlčky (-) musí být bezprostředně před a následované písmenem nebo číslicí. Po sobě jdoucí pomlčky nejsou povolené v názvu kontejneru.</li><li>Název může být dlouhý 3 až 63 znaků.</li></ul> |
| Propojené služby a datové sady a kanály |Je jedinečný s ve službě data factory. Názvy jsou malá a velká písmena. |<ul><li>Názvy objektů musí začínat písmenem, číslo nebo podtržítko (_).</li><li>Nejsou povolené tyto znaky: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li><li>Pomlčky ("-") v názvech propojených služeb a datových sad pouze nejsou povoleny.</li></ul>  |
| Skupina prostředků |Jedinečný v rámci Microsoft Azure. Názvy jsou malá a velká písmena. | Další informace najdete v tématu [Azure – pravidla pojmenování a omezení](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Další postup
Zjistěte, jak vytvářet datové továrny pomocí podrobných pokynů v [rychlý start: vytvoření datové továrny](quickstart-create-data-factory-powershell.md) článku. 
