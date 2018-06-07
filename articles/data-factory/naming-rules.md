---
title: Pravidla pro pojmenovávání entit služby Azure Data Factory | Microsoft Docs
description: Popisuje pravidla pojmenování entit služby Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: e9d2140edc64daca4df0463dbfdcd5b06f072012
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620404"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - pravidla po pojmenování
Následující tabulka obsahuje pravidla pojmenování artefaktů služby Data Factory.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [pojmenování pravidla v objektu pro vytváření dat version1](v1/data-factory-naming-rules.md).

| Název | Jedinečnost názvu | Ověřovací kontroly |
|:--- |:--- |:--- |
| Data Factory |Jedinečná napříč Microsoft Azure. Názvy jsou velká a malá písmena, který je `MyDF` a `mydf` odkazovat na stejné služby data factory. |<ul><li>Každý objekt pro vytváření dat je vázaný na přesně jedno předplatné.</li><li>Názvy objektů musí začínat písmenem nebo číslicí a může obsahovat pouze písmena, číslice a pomlčky (-) znaků.</li><li>Každý znak pomlčka (-) musí být okamžitě a následnou písmenem nebo číslem. Po sobě jdoucí pomlčky nejsou povolené v názvech kontejneru.</li><li>Název může být 3 až 63 znaků dlouhý.</li></ul> |
| Propojených služeb/tabulek/kanálů |Jedinečný s ve službě data factory. Názvy jsou velká a malá písmena. |<ul><li>Maximální počet znaků v názvu tabulky: 260.</li><li>Názvy objektů musí začínat písmenem, číslo nebo podtržítko (_).</li><li>Nejsou povolené tyto znaky: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li><li>Pomlčky ("-") v názvech propojené služby a jenom datové sady nejsou povoleny.</li></ul>  |
| Skupina prostředků |Jedinečná napříč Microsoft Azure. Názvy jsou velká a malá písmena. | Další informace najdete v tématu [Azure pravidla pojmenování a omezení](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Další postup
Naučte se vytvářet datové továrny dodržením podrobných pokynů v [rychlý start: Vytvořte objekt pro vytváření dat](quickstart-create-data-factory-powershell.md) článku. 
