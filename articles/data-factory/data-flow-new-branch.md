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
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606395"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Vytvoření nové větve v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Přidejte novou větev pro více sad operací a transformací proti stejnému datovému proudu. Přidání nové větve je užitečné, pokud chcete použít stejný zdroj pro více jímek nebo pro vlastní spojování dat dohromady.

Novou větev lze přidat ze seznamu transformace podobné jiné transformace. **Nová větev** bude k dispozici pouze jako akce, pokud existuje existující transformace po transformaci, kterou se pokoušíte větev.

![Přidání nové větve](media/data-flow/new-branch2.png "Přidání nové větve")

V níže uvedeném příkladu je tok dat čtení dat o jízdě taxi. Výstup agregovaný podle dne i dodavatele je povinný. Namísto vytváření dvou samostatných datových toků, které se čtou ze stejného zdroje, lze přidat novou větev. Tímto způsobem obě agregace lze provést jako součást stejného toku dat. 

![Přidání nové větve](media/data-flow/new-branch.png "Přidání nové větve")
