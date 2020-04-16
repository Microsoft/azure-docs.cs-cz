---
title: Více větví v mapování toku dat
description: Replikace datových proudů v mapování toku dat s více větvemi
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: af356641b4588529aea25826ff180707ff1ef4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413609"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Vytvoření nové větve v toku dat mapování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Přidejte novou větev pro více sad operací a transformací proti stejnému datovému proudu. Přidání nové větve je užitečné, pokud chcete použít stejný zdroj pro více jímek nebo pro vlastní spojování dat dohromady.

Novou větev lze přidat ze seznamu transformace podobné jiné transformace. **Nová větev** bude k dispozici pouze jako akce, pokud existuje existující transformace po transformaci, kterou se pokoušíte větev.

![Přidání nové větve](media/data-flow/new-branch2.png "Přidání nové větve")

V níže uvedeném příkladu je tok dat čtení dat o jízdě taxi. Výstup agregovaný podle dne i dodavatele je povinný. Namísto vytváření dvou samostatných datových toků, které se čtou ze stejného zdroje, lze přidat novou větev. Tímto způsobem obě agregace lze provést jako součást stejného toku dat. 

![Přidání nové větve](media/data-flow/new-branch.png "Přidání nové větve")
