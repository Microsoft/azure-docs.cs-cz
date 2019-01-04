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
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: daf0b1c12ab10230690a62eb5dc772417d8b92f3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024497"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory – pravidla pojmenování
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [pravidla ve službě Data Factory pojmenování](../naming-rules.md).

Následující tabulka obsahuje pravidla pojmenování artefaktů služby Data Factory.

| Název | Jedinečnost názvu | Ověřovací kontroly |
|:--- |:--- |:--- |
| Data Factory |Jedinečný v rámci Microsoft Azure. Názvy jsou malá a velká písmena, tedy `MyDF` a `mydf` odkazovat na stejnou datovou továrnou. |<ul><li>Každá datová továrna se váže na přesně jedno předplatné Azure.</li><li>Názvy objektů musí začínat písmenem nebo číslicí a může obsahovat jenom písmena, číslice a znak spojovníku (-).</li><li>Každému znaku pomlčky (-) musí být bezprostředně před a následované písmenem nebo číslicí. Po sobě jdoucí pomlčky nejsou povolené v názvu kontejneru.</li><li>Název může být dlouhý 3 až 63 znaků.</li></ul> |
| Propojených služeb/tabulek/kanálů |Je jedinečný s ve službě data factory. Názvy jsou malá a velká písmena. |<ul><li>Maximální počet znaků v názvu tabulky: 260.</li><li>Názvy objektů musí začínat písmenem, číslo nebo podtržítko (_).</li><li>Nejsou povolené tyto znaky: ".", "+","?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul> |
| Skupina prostředků |Jedinečný v rámci Microsoft Azure. Názvy jsou malá a velká písmena. |<ul><li>Maximální počet znaků: 1000.</li><li>Název může obsahovat písmena, číslice a následující znaky: "-", "_",","a"."</li></ul> |

